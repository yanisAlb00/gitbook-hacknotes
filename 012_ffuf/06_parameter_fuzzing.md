# Parameter Fuzzing

## Parameter Fuzzing - GET

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx

## Parameter Fuzzing - POST

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx

id                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]


curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'

<div class='center'><p>Invalid id!</p></div>



## LAB

sudo sh -c 'echo "94.237.53.82 academy.htb" >> /etc/hosts'

sudo sh -c 'echo "94.237.53.82 admin.academy.htb" >> /etc/hosts'


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:30547/admin/admin.php?FUZZ=key > fuzz-parameter-get.txt

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:30547/admin/admin.php?FUZZ=key > fuzz-parameter-get2.txt -fs 768

user                    [Status: 200, Size: 783, Words: 221, Lines: 54, Duration: 2ms]

94.237.53.82:30547