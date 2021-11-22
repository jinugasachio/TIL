# WAF

## 機能
- 悪意あるリクエストのブロック
- カスタムルールに基づいたWebトラフィックのフィルタ
- モニタリングとチューニング
## 全体像
![スクリーンショット 2021-11-22 21 45 31（2）](https://user-images.githubusercontent.com/49634472/142864234-992d4ffc-1e89-4f50-87a5-597451a0ca6f.png)

## 守備範囲
### HTTP floods
  - 多数の端末やBotを使い、ターゲットのWebサーバに正当に見えるHTTP GETまたはPOSTリクエストを大量に実行する攻撃
### Bots and probes
### SQL injection
  - アプリケーションの脆弱性により本来の意図ではない不当なSQL文が作成されてしまい、注入(injection)されることによってDBのデータを不正に操作される攻撃
### XSS
  - 動的なWebアプリケーションの脆弱性を利用して悪意のあるデータを埋め込みスクリプトを実行させる攻撃手法
### LFI（Local File Inclusion）
  - WEB サーバが持っているファイルを不正に参照する攻撃
### RFI（Remote File Inclusion）
  - WEB サーバが持っているファイルではなく、外部のファイルを参照させる攻撃
  - 例えば、攻撃者が作成した任意のスクリプトを実行させるような攻撃
### Application exploits
  - アプリケーションに含まれるセキュリティ脆弱性（セキュリティホール）をつくコードやプログラムを実行する攻撃
### CSRF
  - ログイン情報を狙った攻撃
  - ユーザーが他サイトのログイン状態を保持したまま悪意を持つ攻撃者が作成したページなどにアクセスしてしまうと、情報やリクエストを勝手に送信されてしまう

## ブロック時に任意のページを表示したい（カスタムブロックページ）
- CloudFrontのみ可能
- CloudFrontのCustom Error Responseから設定可能

## WAF Capacity Unit（WCU）
- WAFのRuleに対する処理コストの考え方
- それぞれのルールの中身に応じて処理コストを計上、その合計がWeb ACLのCapacityを超えない範囲でルールを登録する
- Web ACLのWCUの上限は1500
- 文字列のマッチ条件の処理内容でWCUの使用量が変わる

## モニタリング
![スクリーンショット 2021-11-22 22 18 15（2）](https://user-images.githubusercontent.com/49634472/142868584-d7ec3af3-ef80-4149-86c5-55a61fa62f79.png)

## Full logs
![スクリーンショット 2021-11-22 22 22 11（2）](https://user-images.githubusercontent.com/49634472/142869091-956f188c-3b5d-4f74-8fd7-812cf68723ec.png)


## インシデントレポートの例
![スクリーンショット 2021-11-22 22 20 05（2）](https://user-images.githubusercontent.com/49634472/142868776-9c07d447-bec2-4ea3-8285-0120a0d8e48e.png)

## 資料
- [AWS WAF](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/waf-chapter.html)
- [【AWS Black Belt Online Seminar】AWS WAF アップデート](https://youtu.be/4KbCJAjiA3A)
