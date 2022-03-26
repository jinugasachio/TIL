# KMS

### [key policy に root ユーザーの定義が必要なのはなぜか？](https://blog.manabusakai.com/2021/08/review-the-kms-key-policy/)
- 管理者権限を持つ IAM user / role を誤って削除してしまっても root ユーザーから操作することができるようにするため
  - CMK は root ユーザーであっても key policy で明示的に許可しないとアクセスできないので、

- IAMポリシーでも権限を制御できるようにするため
  - root ユーザーに権限を与えるとそのキーへの IAM policy が有効になる

## 資料
- [KMS ベストプラクティス](https://d1.awsstatic.com/whitepapers/ja_JP/aws-kms-best-practices.pdf)