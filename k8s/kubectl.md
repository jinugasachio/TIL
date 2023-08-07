#### deployment に紐づく pod の再起動
```
kubectl rollout restart deploy deployment名
```

#### 複数のマニフェストファイルの適用
```
# dir 配下を適用
kubectl apply -f ./dir

# dir 配下を再起的に適用
kubectl apply -f ./dir -R
```

#### マニフェストの適用（削除されたリソースも検知して削除）
```
kubectl apply --prune
```

#### クラスタの登録情報とマニフェストの差分を確認
```
kubectl diff -f sample-pod.yaml
```

#### リソース種別の一覧取得
```
# Namespace レベルのリソース
kubectl api-resources --namespaced=true

# Cluster レベルのリソース
kubectl api-resources --namespaced=false
```

#### リソースの使用量
```
# node
kubectl top node

# pod
kubectl top pod
```

#### ログ確認
```
# pod のログを確認
kubectl logs sample-pod

# コンテナを絞って確認
kubectl logs sample-pod -c nginx-container

# follow してリアルタイムに確認
kubectl logs -f sample-pod 
```

#### ロールバック
```
# リビジョンを指定してロールバック
kubectl rollout undo deploy sample-deployment --to-revision 1

# 1つ前にロールバック
kubectl rollout undo deploy sample-deployment
```
