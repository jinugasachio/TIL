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

## リバースプロキシの基本設定
### upstream コンテキスト
- バックエンドのアプリケーションサーバーを示すもの
- サーバーのIPアドレスとポートを並べた server ディレクティブを並べて記述しまとめたものに名前をつける
### server コンテキスト
- 外部からのアクセス方法を示す
- リバースプロキシを指定するときは proxy_pass ディレクティブで転送先の upstream の名前を指定する

```
http {
  upstream app1 {
    server 192.168.1.10:8080
    server 192.168.1.11:80
    server 192.168.1.12:8080
  }

  server {
    listen 80;
    location / {
      proxy_pass http://app1
    }
  }
}
```

転送先が1つだけの場合はupstreamを使わずとも直接指定できる

```
http {
  server {
    listen 80;
    location / {
      proxy_pass http://192.168.1.10:8080
    }
  }
}
```


## 良い感じの資料
- [ApacheとNginxの違い](https://openstandia.jp/pdf/140228_osc_seminar_ssof8.pdf)