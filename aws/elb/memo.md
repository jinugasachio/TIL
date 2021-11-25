# ELB
- AWS側で自動でスケールアップ、スケールアウトしてくれる。
  - 急激な負荷だとスケーリングが間に合わない場合があるので、事前にスケールさせておくと良い。
    - Pre-warming（暖機運転）の申請をする。
    - 自前で負荷を段階的にかける。

## 概念
### リスナー
- 受け付けるトラフィックのプロトコルとポート番号。（「listenする」とよく言う）
### ターゲット
- リスナー設定により受け付けたトラフィックを転送するEC2インスタンスなのどのリソースやエンドポイント。
  - インスタンスIDでターゲットを指定可。（グループの場合はグループARN
  、terraformではそうだった。）
  - CLB以外はIPアドレスでターゲットを指定可。
  - ALBはLambda関数をターゲットに指定可。

## ALB
- ラウンドロビンでバックエンドへルーティング。
- クロスゾーン負荷分散がデフォルトで有効。
- スティッキーセッションが利用可、デフォルトでは無効。
  - アプリのセッション情報、一時ファイルなどをインスタンスが保持する構成の場合に必要。→ DBサーバーやキャッシュに持たせるのが一般的なのでこの構成自体は好ましくない。ルールを設定可能。
- HTTPSリスナー設定時のセキュリティーポリシーは`ELBSecurityPolicy-2016-08`がデフォルト & 推奨（[参考](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/create-https-listener.html)）
## NLB
- セキュリティグループ指定できない。
- クロスゾーン負荷分散がデフォルトで無効。
- 暖機運転せずともで数百万リクエスト/秒のトラフィックを捌ける。
## CLB
- EC2-Classsic ネットワーク用。
- 旧世代なので基本的に使用することない。
- クロスゾーン負荷分散がデフォルトで有効。
- スティッキーセッションが利用可、デフォルトでは無効。

## ベストプラクティス
- ターゲットとなるEC2インスタンスのセキュリティグループはELBのセキュリティグループからのトラフィックのみを許可するように設定する。
- SSL/TLS Terminationを使って、`ユーザー → LB`はhttpsで通信、`LB → インスタンス`はhttpで通信。 これによりインスタンスの負荷をオフロードできる。（ALBとCLBのみ？）

## アクセスログ
- アクセスログ設定を有効化。
- 専用のS3バケットを作成する。（SSE-S3 を使用して自動的に暗号化される）
- 対象ELBにバケットポリシーを付与。
  - `principal` でELBのアカウントIDを指定する必要がある。リージョン毎に異なるので注意。（[参考](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/load-balancer-access-logs.html#access-logging-bucket-permissions)）

## Tips
- 複数AZ（＝サブネット）にまたがる場合、論理的には1つのLBがプロビジョニングされているが実体は各AZ（＝サブネット）毎に配置されている。ENIがLB分だけに作成されているのがコンソールから分かる。（[参考](https://dev.classmethod.jp/articles/purge-resources-specific-az/)）

## 良い感じの資料
- [AWS Elastic Load Balancing](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/userguide/what-is-load-balancing.html)
  - [Application Load Balancer](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/introduction.html)
- [AWS Black Belt Online Seminar Elastic Load Balancing (ELB)
](https://youtu.be/4laAoK-zXko)
