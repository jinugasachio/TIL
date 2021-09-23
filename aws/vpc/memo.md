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
<br>
<br>

## 良い感じの資料
### 読み物
- [AWS Amazon VPC ユーザーガイド](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)
### 動画
- [【AWS Black Belt Online Seminar】Amazon VPC 2019](https://youtu.be/aHEVvsk6pkI)
- [【AWS Black Belt Online Seminar】Amazon VPC 2020](https://youtu.be/JAzsGRS_o4c)
- [【AWS Black Belt Online Seminar】 Amazon VPC Advanced](https://youtu.be/WCq_2-zkV44)


