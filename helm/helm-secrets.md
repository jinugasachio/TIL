# [Helm Secrets](https://github.com/jkroepke/helm-secrets)
## secretsの追加方法
※ もちろん公式にも書いてあるんだけどfreeeのリポジトリだとうまくいかなかったのでそれ用のメモ

1. 複合する
```
  SOPS_KMS_ARN={secrets.yamlで指定されていない場合はkmsのarn} helm secrets dec path/to/secrets.yaml
```
2. 上記により同ディレクトリに `secrets.yaml.dec` ができる
3. `secrets.yaml.dec` に平文で `secrets` を追加
4. 暗号化する
```
  SOPS_KMS_ARN={secrets.yamlで指定されていない場合はkmsのarn} helm secrets enc path/to/secrets.yaml
```
5. `secrets.yaml.dec` を元に `secrets.yaml` が暗号化される
6. `secrets.yaml.dec` は不要になるので削除
