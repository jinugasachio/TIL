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
  - バケット設定
    - バージョニング
    - 暗号化
    - パブリックアクセスのブロック
  - state lockを有効にする
- 複数環境（prod, staging etc）がある場合は環境毎に独立したStateファイルで管理する

## アンチパターン
- [1つのリソースのみ含むモジュールを作ってしまう](https://qiita.com/bigwheel/items/2b420183639416b5c6bb#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B31-1%E3%81%A4%E3%81%AE%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%81%AE%E3%81%BF%E5%90%AB%E3%82%80%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%82%92%E4%BD%9C%E3%81%A3%E3%81%A6%E3%81%97%E3%81%BE%E3%81%86)
- [チャイルドモジュールに他のモジュールを含んでしまう](https://qiita.com/bigwheel/items/2b420183639416b5c6bb#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B32-%E3%83%81%E3%83%A3%E3%82%A4%E3%83%AB%E3%83%89%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%81%AB%E4%BB%96%E3%81%AE%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%82%92%E5%90%AB%E3%82%93%E3%81%A7%E3%81%97%E3%81%BE%E3%81%86)
- [tfファイル1つだけのモジュールを作ってしまう / READMEや利用例を書かない](https://qiita.com/bigwheel/items/2b420183639416b5c6bb#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B33-tf%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB1%E3%81%A4%E3%81%A0%E3%81%91%E3%81%AE%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%82%92%E4%BD%9C%E3%81%A3%E3%81%A6%E3%81%97%E3%81%BE%E3%81%86--readme%E3%82%84%E5%88%A9%E7%94%A8%E4%BE%8B%E3%82%92%E6%9B%B8%E3%81%8B%E3%81%AA%E3%81%84)
- [チャイルドモジュール内で provider ブロックを書いてしまう](https://qiita.com/bigwheel/items/2b420183639416b5c6bb#%E3%82%A2%E3%83%B3%E3%83%81%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B34-%E3%83%81%E3%83%A3%E3%82%A4%E3%83%AB%E3%83%89%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E5%86%85%E3%81%A7-provider-%E3%83%96%E3%83%AD%E3%83%83%E3%82%AF%E3%82%92%E6%9B%B8%E3%81%84%E3%81%A6%E3%81%97%E3%81%BE%E3%81%86)
## 良い感じの資料
- [「それ、どこに出しても恥ずかしくないTerraformコードになってるか？」](https://speakerdeck.com/yuukiyo/terraform-aws-best-practices)
- [TerraformかCloudFormationか？失敗例コミでIaCを語る！GUIからの卒業](https://youtu.be/SzrEG5BjnLM)
- [Terraform職人入門: 日々の運用で学んだ知見を淡々とまとめる](https://qiita.com/minamijoyo/items/1f57c62bed781ab8f4d7)
- [Terraform職人再入門2020](https://qiita.com/minamijoyo/items/3a7467f70d145ac03324)
- [Terraform Best Practices](https://www.terraform-best-practices.com/)
