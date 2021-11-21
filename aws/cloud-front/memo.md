# CloudFront
## CDN
### 概要
- 大容量キャパシティを持つ地理的に分散したエッジサーバーからコンテンツをキャッシュしたり代理配信をする
### メリット
- ユーザーを一番近いエッジロケーションに誘導することで配信のレイテンシーを最小にできる
- エッジサーバーでコンテンツのキャッシングを行いオリジンの負荷をオフロード

## 最適なエッジロケーションの割り当て方法
![スクリーンショット 2021-11-21 19 06 32（2）](https://user-images.githubusercontent.com/49634472/142757843-cd7daa58-c8bc-4fdc-9fd2-22f724482690.png)

## コンテンツ配信設定の流れ
![スクリーンショット 2021-11-21 19 22 45（2）](https://user-images.githubusercontent.com/49634472/142758150-d32dbd34-960b-4d7b-8dd9-09ec7e91e91f.png)


## 用語
- リージョナルエッジキャッシュ
  - エッジロケーションとオリジンの間にもう一段キャッシュサーバ郡を組み込んだもの（[参考](https://dev.classmethod.jp/articles/cloudfront-regional-edge-cache/)）
  - [CloudFront とリージョン別エッジキャッシュとの連携](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/HowCloudFrontWorks.html#CloudFrontRegionaledgecaches) 
- ディストリビューション
  - ドメイン毎に割り当てられるCloudFrontの設定

## 資料
- [Amazon CloudFront ドキュメント](https://docs.aws.amazon.com/ja_jp/cloudfront/index.html)
- [AWS Black Belt Online Seminar Amazon CloudFrontの概要](https://youtu.be/mmRKzzOvJJY)