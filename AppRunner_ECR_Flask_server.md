# AWS App Runner で ECR 登録した Flask を動かす

## Usage 

- 1) ECR でリポジトリ作成
- 2) コンテナイメージを push
- 3) App Runner で deploy


## Tips

- Dockerfile のデバッグ
  - ベースイメージをもとに、docker run -it <docker-image> sh でコンテナ内で1行ずつ、メモを取りながら作業
  - 失敗したら exit


## 試したけれど動かしにくかったもの

- ECR 登録せず apprunner.yaml を使わずビルド。
- ECR 登録せず apprunner.yaml を使ってビルド
  - 使用できる OS が Amazon Linux のみ。Chrome と chromedriver のインストールコマンドを書かなければいけないのだが、apprunner.yaml に書いて直接サービスを起動してログを眺めながらデバッグすることに。
- ECR 登録せず apprunner.yaml を使わず docker-compose.yaml でビルド
  - App Runner では複数コンテナを連携することはできないらしい。
  - そもそも App Runner は docker-compose.yaml を使ってビルドすることはできない。




