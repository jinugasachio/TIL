# Docker

- `--volume-driver=local`
  - dockerホストのローカルにボリュームを作成の意
  - ローカルではなく外部のファイルシステムを利用することもできる（[参考](https://docs.docker.com/engine/extend/legacy_plugins/#volume-plugins)）

- `docker system prune -a`
  - 実行中のコンテナを除き、利用されてないイメージやコンテナを一掃する