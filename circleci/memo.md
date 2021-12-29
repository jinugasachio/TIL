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
  