# Container Insights
## 概要
- コンテナ化されたアプリケーションのメトリクスとログを収集、集計、要約できるCloudWatch の機能の1つ
- タスク、コンテナレベルでのモニタリングが可能
- 収集するメトリクスは自動的に作成されるダッシュボードに集約され、より鋭い洞察を行うことが可能
- ECS、EKS、及び EC2 の Kubernetes プラットフォームで利用可能 

## 連携
下記ツールと統合されているのでダッシュボードを起点に要件に応じたより詳細な分析が可能
#### CloudWatch Logs Insights
- グラフのより詳細な値が見たい
- ログに対し分析クエリを発行したい

#### X-Ray
- タスク間の通信をトレースしたい

## ユースケース
### 各コンテナへの適切なリソース配分を行う
![スクリーンショット 2021-11-23 20 38 24（2）](https://user-images.githubusercontent.com/49634472/143017782-03e98974-6dfd-49f7-953a-6017b7fdc491.png)


## 資料
- [【AWS Black Belt Online Seminar】 CON247 メトリクス入門 Container Insights](https://youtu.be/4mmAI8M8wrM)
