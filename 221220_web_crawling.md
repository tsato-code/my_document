# Web サイトのクローリングとスクレイピング


### 準備

```py
import requests
from bs4 import BeautifulSoup
from bs4.element import Comment


url_l = [
    'https://imuraya-cp.jp/reito_wagashi/',                             # 井村屋きなこおはぎ
    'https://www.cas.go.jp/jp/gaiyou/jimu/jinjikyoku/kochikuni.html',   # 内閣人事局プロジェクト
    'https://www.suntory.co.jp/beer/beerball/',                         # サントリー ビアボール
    'https://recruit.kouaikai.net/'                                     # 医療法人社団 鴻愛会
]

# useragent の設定
user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36'
header = {
    'User-Agent': user_agent
}

# 視認タグ判定
# html コードのコメントを除去するのに利用する
def tag_visible(element):
    if element.parent.name in ['style', 'script', 'head', 'title', 'meta', '[document]']:
        return False
    if isinstance(element, Comment):
        return False
    return True
    
```

## クローリング

```py
for i, url in enumerate(url_l):
    print(url)
    response = requests.get(url, header)            # ページをクロール
    response.encoding = response.apparent_encoding  # 文字化け対策

    # 保存
    with open(f'page_{i}.html', 'w', encoding='utf-8') as f:
        f.write(response.text)
```

## スクレイピング

```py
# html ファイルからテキスト抽出
for i in range(len(url_l)):
    with open(f'page_{i}.html', 'r', encoding='utf-8') as f:
        page = f.read()
    
    soup = BeautifulSoup(page, 'lxml')
    texts = soup.find_all(text=True)
    visible_texts = filter(tag_visible, texts)
    filtered_text = ' '.join(t.strip() for t in visible_texts).strip()
    print(filtered_text)
```

