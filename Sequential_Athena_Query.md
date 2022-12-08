# 逐次的に Athena のクエリを流す

SageMaker から Athena のクエリを流すときに、逐次的にクエリを流す方法。


```Python
import time
import pandas as pd
from pyathena import connect
from tqdm.auto import tqdm


BUCKET = 'xxxx'
SOURCE_DB = 'xxxx'
SOURCE_TABLE = 'xxxx'
SINK_DB = 'xxxx'
SINK_TABLE = 'xxxx'
DATE_FROM = 20221201
DATE_TO = 20221231

# Athena の接続
conn = connect(
    s3_staging_dir='s3://{BUCKET}/aws-athena-query-results/',
    region_name='ap-northeast-1',
    # work_group='xxx_xxxx'     # 必要あれば記載
)
cursor = conn.cursor()

# テーブル作成
template_create_table = lambda: f'''
CREATE EXTERNAL TABLE IF EXISTS {SINK_DB}.{SINK_TABLE}(
    id      STRING
  , sales   BIGINT
)
PARTITIONED BY (yyyymmdd INT)
STORED AS PARQUET
LOCATION 's3://{BUCKET}/{SINK_DB}/{SINK_TABLE}'
'''

# データ抽出
template_extract_data = lambda: f'''
INSERT INTO {SINK_DB}.{SINK_TABLE}

SELECT
    id
  , sales
  , yyyymmdd
FROM {SOURCE_DB}.{SOURCE_TABLE}
WHERE yyyymmdd = {yyyymmdd}
'''

# 日付リスト
date_range = pd.date_range(start=str(DATE_FROM), end=str(DATE_TO)).strftime('%Y%m%d')

# クエリリスト
query_l = []
query_l.append( template_create_table() )
for yyyymmdd in date_range:
    query_l.append( template_extract_data() )

# クエリ確認
print(query_l[0])
print(query_l[-1])

# クエリを逐次実行
for query in tqdm(query_l):
    cursor.execute(query)
    time.sleep(1)
```




