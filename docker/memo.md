# Docker
## 公式ドキュメント
- [docker docs](https://matsuand.github.io/docs.docker.jp.onthefly/)

## メモ
- `STOPSIGNAL`
  - `docker stop` を実行した際にコンテナプロセスに送信されるシグナルを指定する
    - デフォルトは `SIGTERM`
  - ```Dockerfile
    STOPSIGNAL SIGINT # SIGTERM の代わりに SIGINT が送られる
    ``` 

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

- `-it`
  - コンテナを端末から操作するためのオプション、慣例句のように使われる
  - `-i` オプション
    - コンテナへの標準入出力及びエラー出力を可能にする
  - `-t` オプション
    - カーソル、エスケープキー、コントロールキーなどで操作を可能にする

## Tips
- `apt-get`を実行する場合、ダウンロード元を日本のサーバーにするとbuildの節約時間を減らすことができる場合がある（[参考](https://genzouw.com/entry/2019/09/04/085135/1718/)）

- `rm -rf /var/lib/apt/lists/*` はキャッシュされている全てのパッケージリストを削除しイメージを軽くしている

- マルチステージビルドではビルドコンテナは破棄されるので `RUN` をまとめる必要はない。可読性を維持しサイズを抑えることが可能

- [Docker Bench for Security](https://github.com/docker/docker-bench-security)
  - Dockerを本番で実行・運用する上で行うべきベストプラクティスをチェックするためのツール

- alpineのdockerコンテナにはデフォルトで `bash` がないので `sh`で入る
  - ```
    $ docker exec -it {CONTAINER} sh
    ```

- コンテナ開発のセキュリティベストプラクティス - [NIST SP800-190](https://www.ipa.go.jp/files/000085279.pdf)

- `ENTRYPOINT` と `CMD`
  - `ENTRYPOINT` と `CMD` の両方が書いてある場合には、`CMD` に書かれている内容が、`ENTRYPOINT` に書いてある command のオプションとして実行されます。
  - `docker run` 時に引数をつけた場合、`CMD` の内容が上書きされ `ENTRYPOINT` に書いてあるcommand が実行されます。

## 関連ツール
- [Trivy](https://github.com/aquasecurity/trivy)
  - オススメのコンテナスキャン
  - CI/CD パイプラインに組み込みやすい、CodeBuildでの利用とか良いかも
- [dockle](https://github.com/goodwithtech/dockle)
  - コンテナベストプラクティスチェックツール
  - [CIS Benchmarks](https://www.nri-secure.co.jp/glossary/cis-benchmarks)におけるDocker関連の項目やDockerベストプラクティスに基づいたチェックができる
- [hadolint](https://github.com/hadolint/hadolint)
  - linter 
- [dockerdot](https://github.com/po3rin/dockerdot)
  - レイヤーの依存関係がわかる 
- [Docker 17.09 からADD/COPY --chownでファイルのオーナーを変更できるようになった](https://qiita.com/minamijoyo/items/c599e81f8803e690f3e1)
- [Dive](https://github.com/wagoodman/dive/tree/main)
  - Dockerイメージ、レイヤーコンテンツを探索し、Docker/OCIイメージのサイズを縮小する方法を発見するためのツール。

## 資料
- ![スクリーンショット 2022-03-30 11 43 25](https://user-images.githubusercontent.com/49634472/160743805-a9696032-5f6c-4b33-9616-aefcc015543d.png)

