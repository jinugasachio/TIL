# TFLint
## Tips
- `deep_check = true` 
  - >Deep Checking uses your provider's credentials to perform a more strict inspection.
For example, if the IAM profile references something that doesn't exist, terraform apply will fail, which can't be found by general validation. Deep Checking solves this problem.
  - 実際にAPI叩いた上で解析してくれる
  - ので、クレデンシャルは渡す必要がある
  - [参考](https://github.com/terraform-linters/tflint-ruleset-aws/blob/master/docs/deep_checking.md)
  - [動作イメージ](https://qiita.com/chaspy/items/00f390b34ae5a9e67390)

- TFLint には Teraaformが内蔵されている
  - >それ故に、TFLint内で使われるTerraformのVersionは、TFLintのVersionに依存するということになります。LintをかけるTerraformのコードのVersionと、TFLint内のTerraformのVersionが一致するようにすることが重要です。一致していない場合、チェック結果が間違う可能性があります。
  - 互換性があるかチェックが必要（[参考](https://github.com/terraform-linters/tflint/blob/master/docs/user-guide/compatibility.md)）
## 資料
- [TfLint](https://github.com/terraform-linters/tflint)