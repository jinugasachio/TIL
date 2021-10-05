# Helm

## 公式

- [HELM Docs](https://helm.sh/docs/)

## 概念
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