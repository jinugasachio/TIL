# Helm

## 公式

- [HELM Docs](https://helm.sh/docs/)

## 概念

- 同じアプリケーションを異なる環境にデプロイする場合、環境に応じてマニフェストファイルをそれぞれ用意するのは大変
- デプロイ先に依存する設定値だけを定義しそれをもとに同じマニフェストファイルでデプロイできる仕組みが熱望されていた
- それを解決するのがHelmである

```
helm（chartを管理） → chart（manifest fileのテンプレートの集合）→ manifest file（k8sリソースを管理）→ k8s
```
### チャート
- k8sのマニフェストのテンプレートをまとめたもの
### コンフィグ
- リリース可能なオブジェクトを作成するためにパッケージ化された チャートにマージできる構成情報
### リリース
- k8s上にインストールされたパッケージ

## アクション
- `range`
  - 引数に渡した配列でループを回します

- `with`
  - 引数に渡したオブジェクトをルートオブジェクトとしたスコープを展開

- `template`
  - `_helpers.tpl` に定義してある名前付きtemplateを展開
  - `include`関数と違いルートオブジェクトを渡すことができない
- `define`
    - 名前付きテンプレートを定義する

## メソッド
- `tuple`
  - 引数の複数の文字列から配列を生成

## サブチャート
### 概要
- 独立したものであり、呼ばれる親チャートに依存しない
- よって、親のvalueを参照することはできない
- 親チャートはサブチャートの値を上書きできる
- 全てのチャートから参照できるものを`global values`としている

## デバック tips
- `helm lint`
  - チャートがベストプラクティスに沿っているかわかる
- `helm install --dry-run --debug` or `helm template --debug`
  - チャートをマニフェストファイルとして出力してくれる
- `helm get manifest`
  - k8sのサーバーに既にインストール済みかどうかわかる

## helm-secrets
- helm-secretsはhelmで[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)を扱いやすくするためのwrapper
- インストール
  - ```
    $ helm plugin install https://github.com/futuresimple/helm-secrets
    ```
- 暗号化する
  - `secrets.yaml` に暗号化したい値を記載
  - `.sops.yaml` に鍵を指定
  - ```
    $ helm secrets enc path/to/secrets.yaml
    ```
  - 詳細はググって！

- 複合化する
  - ```
    $ helm secrets view helm_vars/dev/secrets.yaml
    ```