# apk

- [Alpine Linux](https://alpinelinux.org/) のパッケージマネージャー

- [公式wiki](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management)

## コマンド
- `apk update`
  - ローカルにあるapkのインデックスキャッシュ（`/var/cache/apk`）を更新
  - パッケージ検索やインストールは上記のインデックスの情報をもとに行われる

- `apk search`
  - 利用可能なパッケージを検索する

- `apk add`
  - パッケージをインストールする
  - `--no-cache`
    - インデックスキャッシュを利用せずに新たに取得したインデックスをもとにパッケージをインストールする
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

- `adduser`
    ```
    Usage: adduser [OPTIONS] USER [GROUP]
  
    Create new user, or add USER to GROUP
  
          -h DIR          Home directory
          -g GECOS        GECOS field
          -s SHELL        Login shell
          -G GRP          Group
          -S              Create a system user
          -D              Don't assign a password
          -H              Don't create home directory
          -u UID          User id
          -k SKEL         Skeleton directory (/etc/skel)
    ```