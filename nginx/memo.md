# Nginx
## 公式
- [nginx ドキュメント](http://mogile.web.fc2.com/nginx/)

## 設定
### serverコンテキスト
- listen ディレクティブ
  - 待ち受けるポートを指定する
  - `default_server` 引数を追加するとデフォルトバーチャルホストとして設定される
  - デフォルトバーチャルホストとは、複数のサイトが運用されているウェブサーバーに、ドメイン名ではなくIPアドレスでHTTPリクエストした場合に応答するホストのこと

- server_name ディレクティブ
  - バーチャルホストのサーバー名を指定する

## 良い感じの資料
- [ApacheとNginxの違い](https://openstandia.jp/pdf/140228_osc_seminar_ssof8.pdf)