# ECR

- [ECR Public Gallery](https://gallery.ecr.aws/)を使えばDocker hubを介さずにパブリックなイメージがpullできる
  - AWS上でシステムを構築する際はこちらを使った方が良いのかも
  - [ECRをパブリックレジストリとして利用可能になりました！](https://dev.classmethod.jp/articles/ecr-public-registry/)

- ECRのバックアップは不要かも
  - ソースコードとDockerfileがあれば生成は可能
  - ECR上のコンテナイメージは実態としてはイレブンナインの耐久性であるS3上に保存されている

- 保存容量分だけ料金が発生

- イメージスキャン機能は追加費用なしで利用可能、プッシュ時スキャンを有効化するべし