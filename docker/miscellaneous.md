# Docker

- `--volume-driver=local`
  - dockerホストのローカルにボリュームを作成意味
  - ローカルではなく外部のファイルシステムを利用することもできる（[参考](https://docs.docker.com/engine/extend/legacy_plugins/#volume-plugins)）

- Dockerfileでよく見る`rm -rf /var/lib/apt/lists/*`
  - キャッシュされている全てのパッケージリストを削除しイメージを軽くする