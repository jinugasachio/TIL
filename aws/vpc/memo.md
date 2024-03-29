# VPC
## 分割を検討すべきケース
- アプリケーション毎
- 監査のスコープ
- リスクレベル
- 本番・検証・開発などの環境毎
- 部署毎
- 共通サービスの切り出し

※ AWSアカウントとVPC分割パターンはITオペレーションモデルに沿ったものが必要、そこをよく考えるべし。

<br>

## NAT ゲートウェイ

### ベストプラクティス
- AZ 毎に設置する

### プライベートサブネットからインターネットにアクセスする

#### 前提
- AZ-A のパブリックサブネットには パブリック NAT ゲートウェイ。
- AZ-B のプライベートサブネットにはインスタンス。

#### 結論
- インターネットからインスタンスへはアクセス不可
- インスタンスからインターネットへのアウトバウンドトラフィックは可能。
  - ルーターがインスタンスから NAT ゲートウェイにインターネットバウンドトラフィックを送信。
  - NAT ゲートウェイは、自身の elastic IP アドレスを送信元 IP アドレスとして使用し、インターネットゲートウェイにトラフィックを送信。

#### その他
- ALBがある場合のインスタンスからのアウトバウンドトラフィックは、ALBを経由するのか？と疑問に思ったが、`インスタンス -> ルーター -> NAT -> IG` という流れっぽい。確信は持てていない。
- その辺りのトラフィックはどうやって確認すれば良い？

<img src="https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/images/nat-gateway-diagram.png">  

<br>

## セキュリティグループ
### 概念
- VPC内に作成できるファイアウォール。
- そしてそのVPC内のどのリソースに紐づけるかを設定できる。
  - 例：AというVPC内に、BとCというセキュリティグループがある。
  - BはA内のALBに設定、CはA内のEC2に設定。みたいな。 

### ハマりポイント
- トラフィックソースには別のセキュリティグループを設定することができる。
  - 例：ALBからプライベートなEC2へのトラフィックを許可する際にはEC2のセキュリティグループのインバウンドソースにALBのセキュリティグループを設定する。

## Tips
- マルチAZは2AZではなく３AZにすべき
  - [なぜ「AWS で負荷分散は３AZ にまたがるのがベストプラクティス」と言われるのか 可用性の面から考えてみた](https://dev.classmethod.jp/articles/202008-three-az-load-balancing/) 

## 良い感じの資料
- [AWS Amazon VPC ユーザーガイド](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)
- [AWS Black Belt Online Seminar Amazon VPC 2019](https://youtu.be/aHEVvsk6pkI)
- [AWS Black Belt Online Seminar Amazon VPC 2020](https://youtu.be/JAzsGRS_o4c)
- [AWS Black Belt Online Seminar Amazon VPC Advanced](https://youtu.be/WCq_2-zkV44)


