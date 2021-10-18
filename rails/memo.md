# Rails

- `config/database.yml`より`DATABASE_URL`の方が優先される（[コード](https://github.com/rails/rails/blob/bb1ecdcc677bf6e68e0252505509c089619b5b90/activerecord/lib/active_record/connection_handling.rb#L76)）

- コンストラクタにおけるメモ化( `||=` ) はほとんどの場合不要である from [参考](https://techracho.bpsinc.jp/hachi8833/2020_06_25/74938)


## 良記事
- [Rails開発におけるwebサーバーとアプリケーションサーバーの違い](https://qiita.com/jnchito/items/3884f9a2ccc057f8f3a3)