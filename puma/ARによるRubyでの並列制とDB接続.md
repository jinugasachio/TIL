# [ActiveRecord による Ruby での並列性と DB 接続](https://devcenter.heroku.com/ja/articles/concurrency-and-database-connections) の要約

## 接続プール
- ARは新しいスレッドまたはプロセスが SQL を使用して DB にアクセスしようとした場合にのみ接続を作成
- `database.yml` の `pool` により最大数を設定可能

## マルチスレッドサーバー
- puma を使用している場合は、`pool`​ 値を同等の `ENV[‘RAILS_MAX_THREADS’]`​ に設定することをお勧め
- クラスタモードを使用している場合は各プロセス毎に確保されるため、ワーカープロセスが `ENV[‘RAILS_MAX_THREADS’]`​ を超えない限りはこの設定で十分

## DB 接続の最大数
- DBはアクティブな接続の最大数に達すると新しい接続を受け付けなくなる
- Heroku では DB インスタンスの種類毎に最大接続数が決まっているのでスケールアウト時に超過しないように気をつける

## アクティブな接続の数

```
$ bundle exec rails dbconsole
```
の後に、

```sql
select count(*) from pg_stat_activity where pid <> pg_backend_pid() and usename = current_user;
```

により、接続の数が返される

```
 count
-------
   5
(1 row)
```
