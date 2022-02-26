# Railsにおけるプロセス数とスレッド数

## 考慮点
### CPU数（環境によっては、vCPU数、仮想CPU数）
- CPUの数は、設定可能なプロセス数に影響を及ぼす
- GVLを考慮するとCPUをフル活用するなら、Rubyコードのプロセス数はCPUのコア数に一致させるのがよい（[参考](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server#process-count-value)）


### 利用可能なメモリの量
- スレッドごとにRailsの処理が実行されるので、スレッド数が増えればそれだけ必要なメモリ量も大きくなる
- プロセス数を増やす場合も必要なメモリ量は大きくなる

### 1スレッドで必要なメモリの量
- アプリケーションの実装によって幅が出る
- Railsでは概ね300MB〜1GBくらいの幅で変わることが多い

### Railsで設定しているコネクションプールの数
- コネクションプールとは、RailsがDBにアクセスするたびに接続と切断を行って負荷が高くなったり、パフォーマンスが低下するのを防ぐために、予め決められた上限数を考慮してDBとの間に作っておく接続のグループ
- コネクションプールは各プロセスごとに作られる。プロセス間で共有のプールを持つことはできない
- Herokuではコネクションプール数とスレッド数を同じ値にすることを推奨（[参考](https://devcenter.heroku.com/articles/concurrency-and-database-connections#threaded-servers)）
### webとjobワーカーの数
- DB側に接続上限数が存在するため、jobワーカーが存在する場合どれくらいの数があり、合計何スレッドくらい実行されるか、も考慮にいれる必要がある
### DBの同時接続数上限
- 同時接続数が分かれば全サーバーの全プロセスで作られるスレッド数の合計値がその同時接続数を超えないようにパラメータを調整しなければならない
- 同時接続数は各サーバーのプロセス数やスレッド数の上限値に影響する

## 資料
- [Railsのアプリケーションサーバーのプロセス数とスレッド数の設定方法](https://tech-book.precena.co.jp/software/backend/ruby-on-rails/rails-process-and-thread)