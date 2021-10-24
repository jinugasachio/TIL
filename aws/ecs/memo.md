# ECS


## クラスターにEC2を登録する方法

2パターンある
- クラスター作成時に自動でEC2を作成される。
  - terraformだと対応していないっぽくてハマった。マネコンからのみ。
  - 裏でCloudFormationが働き、クラスターへのEC2の登録からオートスケーリング周りの設定まで自動で行ってくれているっぽい。
- クラスタ作成時にEC2を作成せず手動で登録。
  - Amazon ECS-optimized Amazon Linux 2 AMIを使用。
  - `ecsInstanceRole` という専用のIAMロールを付与。
  - ECSコンテナエージェントに登録先のクラスターを設定。User dataなどに渡しておく。
    ```
    #!/bin/bash
    echo ECS_CLUSTER=your_cluster_name >> /etc/ecs/ecs.config
    ```

## コンテナインスタンスのメモリの見方

<img src="https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/images/instance-memory.png">  

>[Registered (登録済み)] メモリの値は Amazon ECS の初回起動時に登録された時の コンテナインスタンス のメモリの値です。[Available (使用可能)] メモリの値はまだ タスク に割り当てられていないメモリの値です。

つまりタスク定義で設定するメモリはAvailable内に収まらないといけない。

## 動的ポートマッピング
- タスク定義のホストポートを`0`に設定する。
- コンテナインスタンスのセキュリティグループのインバウンドを49153~65535のポート（エフェメラルポート）で受け付けるようにする。（[参考](https://aws.amazon.com/jp/premiumsupport/knowledge-center/dynamic-port-mapping-ecs/)）

## タスク実行ロールとタスクロールの違い
- タスク実行ロール
  - 起動されるECSコンテナエージェントで使用されるロール
- タスクロール
  - タスク（＝コンテナ）で使用されるロール

## ECS on EC2
- コスト
  - EC2インスタンスの起動時間分とEBSボリュームに対して発生
  - キャパシティプランニングを適切にできるなら比較的抑えられる
  - EC2のリソース増強、OS更新・セキュリティパッチ適用を行う必要があるので運用コストは高い
- 拡張性
  - イメージのキャッシュをEC2のホスト上に保持できるので、レジストリからのpullの時間を削減できる、よってデプロイは速い
    - コンテナの起動で時間がかかるのはこのpullの部分
  - キャパシティプランニングの精度によってスケールアウトの拡張性は決まる
  - スケールアップは容易
- 信頼性
  - ECSの障害復旧はAWSの責務
  - EC2の調査は、sshまたはsystem managerを用いてサーバーにログインできるので障害調査は容易


## ベストプラクティス
- クラスターにデフォルトのキャパシティープロバイダー戦略を定義する。

## Tips
- 同一のタスク定義にあるコンテナは、localhostで通信できる
## 良い感じの資料

- [Amazon Elastic Container Service ドキュメント](https://docs.aws.amazon.com/ja_jp/ecs/index.html)
- [AWS Black Belt Online Seminar Amazon ECS](https://youtu.be/tmMLLjQrrRA)
- [AWS Black Belt Online Seminar Amazon ECS Deep Dive](https://youtu.be/3bohQetK2OE)
- [AWS Black Belt Online Seminar CON207 Auto Scaling in ECS 前編](https://youtu.be/FeRkcA33-d0)
- [AWS Black Belt Online Seminar CON207 Auto Scaling in ECS 後編](https://youtu.be/45uuyy16RS4)
- [AWS Black Belt Online Seminar CON202 ECS Fargate入門](https://youtu.be/5fXwkTgWrjw)
- [AWS Black Belt Online Seminar AWS Fargate](https://youtu.be/rwwOoFBq2AU)
