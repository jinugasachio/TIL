# Terraform
## ベストプラクティス
- Terraformのバージョンを固定する
- providerのバージョンを固定する
- 削除操作を抑止する
- コードフォーマットをかける
- バリデーションをかける
- オートコンプリートを有効にする
- プラグインキャッシュを有効にする
- TFLintで不正なコードを検出する
- Stateをリモートで管理
  - StateバケットはTerraformで管理してはいけない
  - 別のAWSアカウントのバケットを利用することがベストプラクティス

## 良い感じの資料
- [「それ、どこに出しても恥ずかしくないTerraformコードになってるか？」](https://speakerdeck.com/yuukiyo/terraform-aws-best-practices)
- [TerraformかCloudFormationか？失敗例コミでIaCを語る！GUIからの卒業](https://youtu.be/SzrEG5BjnLM)