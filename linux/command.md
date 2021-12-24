# コマンド

- `lsof -i:ポート番号`
  - ポート番号から使用しているプログラムを表示

- `pkill 名前`
  - 指定した名前のプロセスを終了させる
  - 下記の場合、`pkill nginx` で全てのプロセスが終了する
  - ```
    $ lsof -i:10254
      COMMAND     PID         USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
      nginx-1.2 15123 ugajin-yukio    6u  IPv4 0x8ca70489ab55b365      0t0  TCP *:10254 (LISTEN)
      nginx-1.2 15124 ugajin-yukio    6u  IPv4 0x8ca70489ab55b365      0t0  TCP *:10254 (LISTEN)
      nginx-1.2 15125 ugajin-yukio    6u  IPv4 0x8ca70489ab55b365      0t0  TCP *:10254 (LISTEN)
    ```

- `test`
  - ファイルの形式のチェックや値の比較を行う
    - ```
      test -d file
      ````
      - file がディレクトリならば真となる
  - よく見かける `if [ -d "$hoge" ]; then` の `[]` は `test` の略式
  - オプションたくさんある
    - https://linuxjm.osdn.jp/html/GNU_sh-utils/man1/test.1.html
    - https://shellscript.sunone.me/if_and_test.html
  - ターミナルで条件を確認したい時
    - 例：`test  -f provider.tf ; echo $?` - カレンとディレクトリにprovider.tfファイルがあるか否か
      - 0なら真、1なら偽

- `uniq`
  - 重複している行を取り除く
  - 元のテキストが“並べ替え済み”であることが前提になるので、必要に応じて先に`sort`コマンドで並べ替えを実行することが多い
  - ただ`awk`を使えば `sort | uniq` みたいなことはやらなくて良い [参考](https://zenn.dev/creationup2u/articles/5981b5ea331455)
    - `awk '!a[$0]++{print}'`

- `set -euo pipefail`
  - `-e`
    - エラー時に実行終了する。これをつけないと、途中でエラーが起きてもスクリプトは最後まで実行されるのでそうしたくないときによく使われる
  - `-o`
    - オプションを指定
    - `pipefail` はその名の通りで、pipe で実行された途中のコマンドが異常終了したときは、コマンド全体の返り値をそれとする
  - `-u` 
    - 特殊変数以外で未定義値を使用した場合にエラーとする