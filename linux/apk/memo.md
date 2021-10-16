# apk

- [Alpine Linux](https://alpinelinux.org/) のパッケージマネージャー

- [公式wiki](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management)

- `apk update`
  - ローカルにあるapkのインデックスキャッシュ（`/var/cache/apk`）を更新
  - パッケージ検索やインストールは上記のインデックスの情報をもとに行われる

- `apk search`
  - 利用可能なパッケージを検索する

- `apk add`
  - パッケージをインストールする
  - `--no-cache`
    - `/var/cache/apk` ディレクトリのインデックスキャッシュを利用せずに新たに取得したインデックスをもとにパッケージをインストールする
    - よって `/var/cache/apk` の中身を削除する必要がなくなるので、なるべくの軽量を目指す `Dockerfile` では頻出
  - `--virtual`
    - 複数のパッケージをひとまとめにし別名をつける
    - `Dockerfile` でビルド時にのみ必要なライブラリ群をひとまとめにしておきビルド完了後にまとめて消す、みたいに使われる
    - ```
      # ビルド時
      $ apk add --no-chache --virtual=build-deps ruby-dev perl-dev

      # ビルド後に下記でruby-dev perl-devをまとめて削除
      $ apk del --no-chache build-deps
       ```

- `apk del`
  - パッケージをアンインストール