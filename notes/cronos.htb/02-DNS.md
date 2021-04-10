# Find DNS Zone Transfers
```bash
$ dig axfr cronos.htb @10.10.10.13
; <<>> DiG 9.16.12-Debian <<>> axfr cronos.htb @10.10.10.13
;; global options: +cmd
cronos.htb.		604800	IN	SOA	cronos.htb. admin.cronos.htb. 3 604800 86400 2419200 604800
cronos.htb.		604800	IN	NS	ns1.cronos.htb.
cronos.htb.		604800	IN	A	10.10.10.13
admin.cronos.htb.	604800	IN	A	10.10.10.13
ns1.cronos.htb.		604800	IN	A	10.10.10.13
www.cronos.htb.		604800	IN	A	10.10.10.13
cronos.htb.		604800	IN	SOA	cronos.htb. admin.cronos.htb. 3 604800 86400 2419200 604800
;; Query time: 28 msec
;; SERVER: 10.10.10.13#53(10.10.10.13)
;; WHEN: Sat Mar 27 18:22:09 EDT 2021
;; XFR size: 7 records (messages 1, bytes 203)
```
axfr - find zone transfers
@ - dns server ip
Found admin.cronos.htb