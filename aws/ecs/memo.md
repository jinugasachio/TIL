# ECS


### クラスターにEC2を登録する方法

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

### コンテナインスタンスのメモリの見方

<img src="https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/images/instance-memory.png">  

>[Registered (登録済み)] メモリの値は Amazon ECS の初回起動時に登録された時の コンテナインスタンス のメモリの値です。[Available (使用可能)] メモリの値はまだ タスク に割り当てられていないメモリの値です。

つまりタスク定義で設定するメモリはAvailable内に収まらないといけない。

### 動的ポートマッピング
- タスク定義のホストポートを`0`に設定する。
- コンテナインスタンスのセキュリティグループのインバウンドを49153~65535のポート（エフェメラルポート）で受け付けるようにする。（[参考](https://aws.amazon.com/jp/premiumsupport/knowledge-center/dynamic-port-mapping-ecs/)）
## ベストプラクティス
- クラスターにデフォルトのキャパシティープロバイダー戦略を定義する。
## 良い感じの資料
### 読み物
- [Amazon Elastic Container Service ドキュメント](https://docs.aws.amazon.com/ja_jp/ecs/index.html)
### 動画