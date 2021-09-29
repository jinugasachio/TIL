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
- k8sが管理する全ての要素や管理者の設定が格納されている。
- etcdの「望ましい状態」がいつも正しい。
#### kube-controller-manager
- k8sオブジェクトを処理するコントローラというコンポーネントを統括管理・実行する。
#### kube-scheduler
- Podをワーカーノードへと割り当てる処理をする。
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
- 作成時に、Cluster IPという固定IPアドレスが割り当てられる。
- Cluster IPにより要求を受信しそれをPodに振り分けるので、プロキシやNATを用いたロードバランサーに相当する。
- CoreDNS(kube-dns)
  - k8sの規定のDNSサービス。
  - Serviceの名前とCluster IPを紐づけている。
- ヘッドレスサービス
  - Cluster IPを設定しないService。
  - 直にPodと通信する。
### ReplicaSet
- 同じ構成のPodを指定した数だけ管理する。
### Deployment
- ReplicaSetを管理し、Podのバージョンアップなどのデプロイを行う。
### StatefulSet
### Job
### CronJob
### Daemonset
### Ingress
### Namespace

## その他の概念
### ラベル
### セレクター