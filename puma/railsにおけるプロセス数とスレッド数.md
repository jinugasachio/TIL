# Railsにおけるプロセス数とスレッド数

## 考慮点
### CPU数（環境によっては、vCPU数、仮想CPU数）
- CPUの数は、設定可能なプロセス数に影響を及ぼす
- GVLを考慮するとCPUの機能をフル活用するなら、Rubyコードのプロセス数は、CPUのコア数に一致させるのがよい（[参考](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server#process-count-value)）


### 利用可能なメモリの量
- スレッドごとにRailsアプリケーションの処理が実行されるので、スレッド数が増えればそれだけ必要なメモリ量も大きくなる
- プロセス数を増やす場合も必要なメモリ量は大きくなる

### 1スレッドで必要なメモリの量
- アプリケーションの実装によって幅が出る
- Railsでは概ね300MB〜1GBくらいの幅で変わることが多い

### Railsで設定しているコネクションプールの数
- コネクションプールとは、Railsの処理がデータベースにアクセスするたびにコネクション接続と切断を行って負荷が高くなったり、パフォーマンスが低下するのを防ぐために、予め決められた上限数を考慮してデータベースとの間に作っておく接続のグループのこと
- コネクションプールは、各プロセスごとに作られます。プロセス間で共有のプールを持つことはできない
- コネクションプール数とスレッド数を同じ値にすることを推奨（[参考](https://devcenter.heroku.com/articles/concurrency-and-database-connections#threaded-servers)）
### webとjobワーカーの数
### データベースの同時接続数上限


## 資料
- [Railsのアプリケーションサーバーのプロセス数とスレッド数の設定方法](https://tech-book.precena.co.jp/software/backend/ruby-on-rails/rails-process-and-thread)