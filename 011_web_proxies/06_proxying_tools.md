# Proxying Tools

## Proxychains

sudo vi /etc/proxychains.conf

#socks4         127.0.0.1 9050
http 127.0.0.1 8080

We should also enable Quiet Mode to reduce noise by un-commenting quiet_mode

proxychains curl http://SERVER_IP:PORT

## Nmap

nmap -h | grep -i prox

nmap --proxies http://127.0.0.1:8080 SERVER_IP -pPORT -Pn -sC

## Metasploit

msfconsole
use auxiliary/scanner/http/robots_txt
set PROXIES HTTP:127.0.0.1:8080
set RHOST SERVER_IP
set RPORT PORT
run

## LAB

use auxiliary/scanner/http/http_put
set PROXIES HTTP:127.0.0.1:8080
set RHOST 94.237.54.123
set RPORT 47650
run

BURP :

PUT /msf_http_put_test.txt HTTP/1.1
Host: 94.237.54.123:47650
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 13.1; rv:108.0) Gecko/20100101 Firefox/108.0
Content-Type: text/plain
Content-Length: 13
Connection: close

msf test file