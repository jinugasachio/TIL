# WAF
## 機能
- 悪意あるリクエストのブロック
- カスタムルールに基づいたWebトラフィックのフィルタ
- モニタリングとチューニング

## WAFの守備範囲
#### HTTP floods
  - 多数の端末やBotを使い、ターゲットのWebサーバに正当に見えるHTTP GETまたはPOSTリクエストを大量に実行する攻撃
#### Bots and probes
#### SQL injection
  - アプリケーションの脆弱性により本来の意図ではない不当なSQL文が作成されてしまい、注入(injection)されることによってDBのデータを不正に操作される攻撃
#### XSS
  - 動的なWebアプリケーションの脆弱性を利用して悪意のあるデータを埋め込みスクリプトを実行させる攻撃手法
#### LFI（Local File Inclusion）
  - WEB サーバが持っているファイルを不正に参照する攻撃
#### RFI（Remote File Inclusion）
  - WEB サーバが持っているファイルではなく、外部のファイルを参照させる攻撃
  - 例えば、攻撃者が作成した任意のスクリプトを実行させるような攻撃
#### Application exploits
  - アプリケーションに含まれるセキュリティ脆弱性（セキュリティホール）をつくコードやプログラムを実行する攻撃
#### CSRF
  - ログイン情報を狙った攻撃
  - ユーザーが他サイトのログイン状態を保持したまま悪意を持つ攻撃者が作成したページなどにアクセスしてしまうと、情報やリクエストを勝手に送信されてしまう
## 資料
- [AWS WAF](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/waf-chapter.html)
- [【AWS Black Belt Online Seminar】AWS WAF アップデート](https://youtu.be/4KbCJAjiA3A)
