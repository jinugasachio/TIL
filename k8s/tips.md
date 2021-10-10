# Kubernetes Tips
- Podに割り振られた仮想IPアドレスはそのPodに所属するすべてのコンテナと共有される
- ReplicaSetを直接扱わずにDeploymentのマニフェストファイルを扱う運用にすることがほとんど
- PodのラベルがServiceにセレクタで定義しているラベルと合致した場合、対象のPodはそのServiceのターゲットになる
- kubectxで操作対象クラスターの切り替えができる
   ```
    $ kubectx docker-desktop 
    Switched to context "docker-desktop".
    ```
- kubensでデフォルトのNamespaceを設定できる
  ```
  $ kubens kube-system
  Context "docker-desktop" modified.
  Active namespace is "kube-system".
  ```

- 