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
  - よく見かける `if [ -d "$hoge" ]; then` の `[]` は`test`の略式
  - オプションたくさんある
    - https://linuxjm.osdn.jp/html/GNU_sh-utils/man1/test.1.html
    - https://shellscript.sunone.me/if_and_test.html

- `uniq`
  - 重複している行を取り除く
  - 元のテキストが“並べ替え済み”であることが前提になるので、必要に応じて先に`sort`コマンドで並べ替えを実行することが多い
