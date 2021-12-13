# TFLint
## Tips
- `deep_check = true` 
  - >Deep Checking uses your provider's credentials to perform a more strict inspection.
For example, if the IAM profile references something that doesn't exist, terraform apply will fail, which can't be found by general validation. Deep Checking solves this problem.
  - 実際にAPI叩いた上で解析してくれる
  - ので、クレデンシャルは渡す必要がある
  - [参考](https://github.com/terraform-linters/tflint-ruleset-aws/blob/master/docs/deep_checking.md)
## 資料
- [TfLint](https://github.com/terraform-linters/tflint)