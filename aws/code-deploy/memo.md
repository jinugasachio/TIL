# CodeDeploy
## デプロイ設定
- Canary（カナリア）
  - トラフィックは 2 回の増分で移行されます。更新された Amazon ECS タスクセットに、2 回目の増分で移行される前に最初の増分および間隔 (分単位) で移行される、トラフィックの割合 (%) が指定されています
- Linear（リニア、線形）
  - トラフィックは毎回同じ間隔 (分) の等しい増分で移行されます。増分ごとに移行するトラフィックの割合 (%) と、増分間の間隔 (分) を指定する
- オールアットワンス
  - 名前の通り全てのトラフィックを一気に移行する

## ECS Blue/Green デプロイ
- GreenタスクをプロビジョニングしLBのトラフィックを切り替え
- 検証Hookにより各ステージのデプロイメントでテストを有効化
- Hookが失敗した場合やCloudWatchアラームを検出したい場合は数秒でBlueタスクにロールバック

## 資料
- [AWS CodeDeploy](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/welcome.html)
- [【AWS Black Belt Online Seminar】AWS CodeDeploy](https://youtu.be/cXa2S2kS0TU)
