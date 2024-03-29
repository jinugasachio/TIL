# Puma
## 概要
- マルチプロセス＆マルチスレッドで動かせる
- Unicorn, Passengerと同様に`fork` を使う設計
- `fork`
  - プロセスが自身の複製を作成して新たなプロセスとして起動すること
  - 元のプロセスを親プロセス、新たに生成されたプロセスを子プロセスという
  - フォークされた瞬間は親子で同じ処理を実行しているが、一般的にはその後子プロセスは自身が子プロセスであることを認識して親とは異なる処理を開始する

## 設定値
### `workers`
- ワーカー数を指定するとclustered modeになる
- master processからworkerをforkする
- workerプロセスはそれぞれスレッドプールを持つ

### `preload_app!`
- 全てのアプリコードをfork前にロードする。これによりRuby 2.0+の場合、OSのcopy-on-writeが効く。そのためメモリ使用量が下がる
- Cluster modeでしか使えない
- phased-restartとpreloadは同時には使えない
## プロセスとスレッド
- プロセスはOSで実行中のプログラムのことで、プロセス内には1つ以上のスレッドが含まれる
- CPUのコアに命令をしているのがスレッド
- 複数のプロセス同士は、同じメモリ領域を共有できない。しかし、同じプロセス内のスレッド同士は、同じメモリ領域を共有できるので、プロセス内に複数のスレッドを作成した方がメモリの利用効率が上がる
- 複数のスレッドが同じメモリ領域を共有すると、メモリの利用効率は上がりますが、誤って別のスレッドに影響する情報を書き換えてしまう可能性もある。いわゆるスレッドセーフではない状態。

### シングルプロセスマルチスレッドのメリット
- 一部のスレッドがIO待ち（例えば、データベースへのリクエストとそのレスポンスを待っているような状態）になってしまっても、プロセス内の他のスレッドで別の処理を進めることができる
- これにより、プロセスとしてのスループットが大きくなる
- もし、1つのプロセスで1つのスレッドしか用意しなかったとしたら、そのスレッドでIO待ちが発生すると処理がブロックしてしまう
## Puma worker killer
- Pumaのworkerは起動して暫く経つとメモリの消費が膨れ上がるため、定期的にworkerを立ち上げ直す