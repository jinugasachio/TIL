 ホストゾーンの全レコードを取得
 ```shell
$ dig sandbox-fs.com any

; <<>> DiG 9.10.6 <<>> sandbox-fs.com any
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 22773
;; flags: qr rd ra; QUERY: 1, ANSWER: 10, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;sandbox-fs.com.			IN	ANY

;; ANSWER SECTION:
sandbox-fs.com.		60	IN	A	18.205.54.242
sandbox-fs.com.		60	IN	A	54.243.163.98
sandbox-fs.com.		60	IN	A	3.212.3.232
sandbox-fs.com.		21600	IN	NS	ns-1469.awsdns-55.org.
sandbox-fs.com.		21600	IN	NS	ns-1992.awsdns-57.co.uk.
sandbox-fs.com.		21600	IN	NS	ns-290.awsdns-36.com.
sandbox-fs.com.		21600	IN	NS	ns-786.awsdns-34.net.
sandbox-fs.com.		900	IN	SOA	ns-786.awsdns-34.net. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
sandbox-fs.com.		300	IN	CAA	0 issue "amazon.com"
sandbox-fs.com.		300	IN	CAA	0 issuewild "amazon.com"

;; Query time: 25 msec
;; SERVER: 192.168.216.1#53(192.168.216.1)
;; WHEN: Mon Jun 19 15:16:06 JST 2023
;; MSG SIZE  rcvd: 351

 ```
