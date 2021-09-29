# Kubernetes
## 構成
### クラスター
- k8sシステムを構成するサーバー群。（マスターノード + ワーカーノード）
### マスターノード
#### kube-apiserver
- 外部とやりとりするプロセス。
- kubectlの指示の先はこのお方。
#### etcd
- クラスターの情報を全て管理しているKVS。
- etcdの「望ましい状態」がいつも正しい。
#### kube-controller-manager
- k8sオブジェクトを処理するコントローラというコンポーネントを統括管理・実行する。
#### kube-scheduler
- Podをワーカーノードへと割り当てる。
#### clound-controller-manager
- k8sを運用する各クラウドサービスと連携し、クラウド上で必要となるモノ（サービス）を作る。
### ワーカーノード
#### kube-let
- kube-schedulerと連携し、ワーカーノード上にPodを配置し、Pod内のコンテナを実行する。
- Podの状態の監視も行い、kube-schedulerに通知もする。
#### kebe-proxy
- ネットワーク通信をルーティングする。

## オブジェクト
### Pod
- k8sにおける最小実行単位。
- 1つ以上のコンテナ、いくつかのボリュームを含むことができる。
  - 大抵は1Podに1コンテナ。
  - ボリュームを共有して何かやりとりする場合は同じPodに入れ、そうでなく通信でやりとりするものは別Podにする。
### Service
- 配下に同一Podを束ねている概念。
- 作成時にCluster IPという固定IPアドレスが割り当てられる。
- Cluster IPにより要求を受信しそれをPodに振り分けるので、プロキシやNATを用いたロードバランサーに相当する。
- CoreDNS(kube-dns)
  - k8sの規定のDNSサービス。
  - Serviceの名前とCluster IPを紐づけている。
- ヘッドレスサービス
  - Cluster IPを設定しないService。
  - 直にPodと通信する。
- 外部への公開方法
  - ワーカーノードのIPを使う（NordPort）。
  - 前段にロードバランサーを配置。
  - 前段にIngressオブジェクトを配置。

### ReplicaSet
- 同じ構成のPodを指定した数だけ維持し続ける。
- 自動復旧されるのはこれのおかげ。
### Deployment
- ReplicaSetを管理し、Podのバージョンアップなどのデプロイを行う。
### StatefulSet
### Job
### CronJob
### Daemonset
### Ingress
- HTTP/HTTPS専用のアプリケーションレイヤーで動作するリバースプロキシ。Serviceの前段に設置される。
- パスによる振り分けができる。
### Namespace
- クラスターを区切る概念。複数のシステムがクラスター上で動いておりそれらをグループ分けしたいときに使う。

## その他の概念
### ラベル
### セレクター