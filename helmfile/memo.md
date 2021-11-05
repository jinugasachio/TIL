# Helmfile
## 概要
- 複数の Helm Chart をまとめて Kubernetes cluster にデプロイするツール
- Helm のラッパーとなっており、複数の Chart から構成されるアプリを効率よくデプロイできる
- Helm Chart のインストール順、依存関係の管理が可能
- デプロイ前後のフックでなにかの処理を実行したりすることも可能