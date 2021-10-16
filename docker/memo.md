# Docker
## 公式ドキュメント
- [docker docs](https://matsuand.github.io/docs.docker.jp.onthefly/)

## ベストプラクティス
- 不要な特権を避ける
  - コンテナを root で実行しないようにする
  - 特定のUIDにバインドしない
  - 実行可能ファイルを root が所有し、書き込み可能ではないものする
- 攻撃対象を減らす
  - マルチステージビルドを活用する
  - distroless イメージを使うか、ゼロからビルドする
  - イメージを頻繁に更新する
  - 暴露されたポートに注意する
- 機密データの漏洩を防ぐ
  - Dockerfileの命令にシークレットや認証情報を入れないようにしましょう
  - ADDよりもCOPYを優先してください
  - Dockerのコンテキストを意識し、.dockerignoreを使用する
- その他
  - レイヤーの数を減らし、インテリジェントに並べる
  - メタデータとラベルを追加する
  - lintersを活用してチェックを自動化する
  - 開発中にイメージをローカルでスキャンする
- イメージのビルドの先には
  - docker socketの保護とTCP接続
  - イメージを署名付きにし、ランタイム時に検証する
  - タグのミュータビリティを避ける
  - 環境を root で実行しない
  - ヘルスチェックを含める
  - アプリケーションの機能を制限する

from  [Dockerfileのベストプラクティス Top 20](https://sysdig.jp/blog/dockerfile-best-practices/)
## アンチパターン
- `.dockerignore`に必要なものだけでなく汎用的なものを列挙すること（[参考](https://qiita.com/munisystem/items/b0f08b28e8cc26132212#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)）

- `latest`タグを使っての運用。コミットのハッシュやバージョン番号のような一意のタグを付けて運用すべし

## コマンド関連
- `--volume-driver=local`
  - dockerホストのローカルにボリュームを作成の意
  - ローカルではなく外部のファイルシステムを利用することもできる（[参考](https://docs.docker.com/engine/extend/legacy_plugins/#volume-plugins)）

- `docker system prune -a`
  - 実行中のコンテナを除き、利用されてないイメージやコンテナを一掃する

- `docker image history`
  - 各レイヤーの命令とファイルサイズを確認できる、軽量化作業には必須

## Tips
- `apt-get`を実行する場合、ダウンロード元を日本のサーバーにするとbuildの節約時間を減らすことができる場合がある（[参考](https://genzouw.com/entry/2019/09/04/085135/1718/)）

- `rm -rf /var/lib/apt/lists/*` はキャッシュされている全てのパッケージリストを削除しイメージを軽くしている

- マルチステージビルドではビルドコンテナは破棄されるので `RUN` をまとめる必要はない。可読性を維持しサイズを抑えることが可能

- [Docker Bench for Security](https://github.com/docker/docker-bench-security)
  - Dockerを本番で実行・運用する上で行うべきベストプラクティスをチェックするためのツール
