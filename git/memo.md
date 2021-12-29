### `pull/xxx/head`
- PR作成時に Github 上に作成されるブランチ
- PRを送ったブランチの head

### `pull/xxx/merge`
- PR作成時に Github 上に作成されるブランチ
- PRを送ったブランチとマージ対象のブランチのマージ後の状態を持つブランチ

### curl で API叩く
```
# PRにコメントする
$ curl \
  -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -u jinugasachio:$token \
  https://api.github.com/repos/jinugasachio/terraform-workspace-type/issues/11/comments \
  -d '{"body":"API直接叩いたよ"}'

# PRにラベルつける
$ curl \
  -X POST \
  -H "Accept: application/vnd.github.v3+json" \
  -u jinugasachio:$token \
  https://api.github.com/repos/jinugasachio/terraform-workspace-type/issues/11/labels \
  -d '[{"name":"bug"}]' \
```