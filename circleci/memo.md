# CircleCI
## コマンド
- ```
  circleci config validate  # 設定ファイルのバリデーションチェック
  ```
- ```
  circleci local execute` # ローカルで実行
  ```

## Tips
- 下記は全て展開されない。envはvalueをそのまま文字列で設定すべし。
  - ```yml
    environment:
      PULL_REQUEST_NUMBER: ${CIRCLE_PULL_REQUEST##*/}
      REPOSITORY_OWNER: $CIRCLE_PROJECT_USERNAME
    ```

  - 例えば上記のようにしていた場合 `$ echo $REPOSITORY_OWNER` とすると `$CIRCLE_PROJECT_USERNAME` とそのまま返ってくる。

- `{{ checksum "ファイル名" }}`
  - `ファイル` の内容を表す一意の文字列を作成
  - そもそも[チェックサムという仕組み](https://ja.wikipedia.org/wiki/%E3%83%81%E3%82%A7%E3%83%83%E3%82%AF%E3%82%B5%E3%83%A0)がある
  - [cksum](https://webkaru.net/linux/cksum-command/) コマンド

## 資料
- [CircleCIドキュメント](https://circleci.com/docs/ja/)
- [依存関係のキャッシュ](https://circleci.com/docs/ja/2.0/caching/#)