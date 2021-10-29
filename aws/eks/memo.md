# EKS

- `aws eks update-kubeconfig --name {cluster_name}`
  - 特定のクラスターのkubeconfig(.kube/config)を作成・または更新する
  - `kubectl config use-context` でそのクラスターを選択できるようになる
