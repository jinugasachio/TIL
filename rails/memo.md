# Rails

- `config/database.yml`より`DATABASE_URL`の方が優先される（[コード](https://github.com/rails/rails/blob/bb1ecdcc677bf6e68e0252505509c089619b5b90/activerecord/lib/active_record/connection_handling.rb#L76)）
  - [Railsガイド 接続設定](https://railsguides.jp/configuring.html#%E6%8E%A5%E7%B6%9A%E8%A8%AD%E5%AE%9A) 
    - > 提供された複数の情報が重複ではなく競合している場合も、常に環境変数の接続設定が優先されます。
    - > `ENV['DATABASE_URL']`の情報よりも`database.yml`の情報を優先する唯一の方法は、`database.yml`で"url"サブキーを用いて明示的にURL接続を指定することです。
         ```
         $ cat config/database.yml
         development:
           url: sqlite3:NOT_my_database

         $ echo $DATABASE_URL
         postgresql://localhost/my_database

         $ rails runner 'puts ActiveRecord::Base.configurations'
         #<ActiveRecord::DatabaseConfigurations:0x00007fd50e209a28>

         $ rails runner 'puts ActiveRecord::Base.configurations.inspect'
         #<ActiveRecord::DatabaseConfigurations:0x00007fc8eab02880
         @configurations=[
           #<ActiveRecord::DatabaseConfigurations::UrlConfig:0x00007fc8eab020b0
              @env_name="development", @spec_name="primary",
              @config={"adapter"=>"sqlite3", "database"=>"NOT_my_database"}
              @url="sqlite3:NOT_my_database">
         ]
        ```

 

- コンストラクタにおけるメモ化( `||=` ) はほとんどの場合不要である from [参考](https://techracho.bpsinc.jp/hachi8833/2020_06_25/74938)

- `bundle install -j4` で4並列でのbundle installが実行される from [参考](https://qiita.com/camelmasa/items/5ca27ab398f105f86c76)

- `gem update --system` は RubyGems(gemコマンド)自体のバージョンアップをする

- `gem update` はインストールされている各gemのバージョンアップをする

## 良い感じの資料
- [Rails開発におけるwebサーバーとアプリケーションサーバーの違い](https://qiita.com/jnchito/items/3884f9a2ccc057f8f3a3)
