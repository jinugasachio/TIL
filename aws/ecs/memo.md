# ECS


### クラスターにEC2を登録する方法

以下のどちらか。
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

## ベストプラクティス
- クラスターにデフォルトのキャパシティープロバイダー戦略を定義する。
## 良い感じの資料
### 読み物
- [Amazon Elastic Container Service ドキュメント](https://docs.aws.amazon.com/ja_jp/ecs/index.html)
### 動画