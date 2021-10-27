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