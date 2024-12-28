# Environment Enumeration

https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

https://github.com/rebootuser/LinEnum

## OS version

cat /etc/os-release

NAME="Ubuntu"
VERSION="20.04.4 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.4 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal

## PATH

echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

## Environment variables

env

SHELL=/bin/bash
PWD=/home/htb-student
LOGNAME=htb-student
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/htb-student
LANG=en_US.UTF-8

<SNIP>

## Kernel version

uname -a

Linux nixlpe02 5.4.0-122-generic #138-Ubuntu SMP Wed Jun 22 15:00:31 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

OR

cat /proc/version

## Physical host

lscpu 

Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   43 bits physical, 48 bits virtual
CPU(s):                          2
On-line CPU(s) list:             0,1
Thread(s) per core:              1
Core(s) per socket:              2
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       AuthenticAMD
CPU family:                      23
Model:                           49
Model name:                      AMD EPYC 7302P 16-Core Processor
Stepping:                        0
CPU MHz:                         2994.375
BogoMIPS:                        5988.75
Hypervisor vendor:               VMware

## Login shells

cat /etc/shells

/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen

## Check defenses

Exec Shield
iptables
AppArmor
SELinux
Fail2ban
Snort
Uncomplicated Firewall (ufw)

## Block devices on the system

lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0   55M  1 loop /snap/core18/1705
loop1                       7:1    0   69M  1 loop /snap/lxd/14804
loop2                       7:2    0   47M  1 loop /snap/snapd/16292
loop3                       7:3    0  103M  1 loop /snap/lxd/23339
loop4                       7:4    0   62M  1 loop /snap/core20/1587
loop5                       7:5    0 55.6M  1 loop /snap/core18/2538
sda                         8:0    0   20G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   19G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   18G  0 lvm  /
sr0                        11:0    1  908M  0 rom 

## Mounted and unmounted drives

cat /etc/fstab

/dev/disk/by-id/dm-uuid-LVM-BdLsBLE4CvzJUgtkugkof4S0dZG7gWR8HCNOlRdLWoXVOba2tYUMzHfFQAP9ajul / ext4 defaults 0 0
/dev/disk/by-uuid/20b1770d-a233-4780-900e-7c99bc974346 /boot ext4 defaults 0 0

## Routing

route

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    0      0        0 ens192
10.129.0.0      0.0.0.0         255.255.0.0     U     0      0        0 ens192

## DNS resolution

cat /etc/resolv.conf

Use internal DNS ?

## ARP Table to see what other hosts the target has been communicated with

arp -a

_gateway (10.129.0.1) at 00:50:56:b9:b9:fc [ether] on ens192

## Existing Users

cat /etc/passwd | cut -f1 -d:

## Check if users have login shells

grep "*sh$" /etc/passwd

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
bjones:x:1001:1001::/home/bjones:/bin/sh

## Existing Groups

cat /etc/group

root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,htb-student

## Group members

getent group sudo

sudo:x:27:mrb3n

ls /home

administrator.ilfreight  bjones       htb-student  mrb3n   stacey.jenkins
backupsvc                cliff.moore  logger       shared

## Mounted File Systems

df -h

Some file system are unmounted because they containfiles, scripts, documents, and other important information must not be mounted and viewed by a standard user. 
Therefore, if we can extend our privileges to the root user, we could mount and read these file systems ourselves.


## Unmounted File Systems

find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student

## All Hidden Files

find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student

## All Hidden Directories

find / -type d -name ".*" -ls 2>/dev/null

## Temporary Files

ls -l /tmp /var/tmp /dev/shm

## LAB

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep flag

getent group sudo
sudo:x:27:lab_adm

$ sudo -su lab_adm  
[sudo] password for htb-student: 
Sorry, user htb-student is not allowed to execute '/bin/sh' as lab_adm on ubuntu.
$ whoami
htb-student
$ sudo su
[sudo] password for htb-student: 
Sorry, user htb-student is not allowed to execute '/usr/bin/su' as root on ubuntu.

ls -l /tmp /var/tmp /dev/shm
/dev/shm:
total 0

/tmp:
total 20
drwx------ 3 root root 4096 Jan 22 12:10 snap-private-tmp
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-logind.service-ugmceg
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-resolved.service-KNhsVg
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-timesyncd.service-z9HGcf
drwx------ 2 root root 4096 Jan 22 12:10 vmware-root_738-2999591909

/var/tmp:
total 12
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-logind.service-9TWo2e
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-resolved.service-KXvsGi
drwx------ 3 root root 4096 Jan 22 12:10 systemd-private-ee92b46689a84ee19437a41719a8c73d-systemd-timesyncd.service-AUMXef

cat /etc/fstab | grep -v "#" | column -t
/dev/disk/by-id/dm-uuid-LVM-zoHZXlYbLjLFMhq6Aiv0porczzq5I1KtfvSvv1v2Taf4fTvbOuSWdaCoIPK07t2M  /      ext4  defaults  0  0
/dev/disk/by-uuid/729deb8f-f6f4-4f20-9aa7-f6baecf003a1                                        /boot  ext4  defaults  0  0

cat /dev/disk/by-id/dm-uuid-LVM-zoHZXlYbLjLFMhq6Aiv0porczzq5I1KtfvSvv1v2Taf4fTvbOuSWdaCoIPK07t2M
Permission denied

cat: /dev/disk/by-uuid/729deb8f-f6f4-4f20-9aa7-f6baecf003a1: Permission denied


cat /tmp/snap-private-tmp
cat: /tmp/snap-private-tmp: Permission denied

curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh > linpeas.sh

scp linpeas.sh htb-student@$TARGET:/tmp/linpeas.sh
HTB_@cademy_stdnt!

cd /tmp
./linpeas.sh
-sh: 42: ./linpeas.sh: Permission denied
chmod 777 linpeas.sh
./linpeas.sh

cat /home/lab_adm/.sudo_as_admin_successful

sudo cat /tmp/snap-private-tmp

lsblk
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0  55.7M  1 loop /snap/core18/2751
loop1                       7:1    0  55.7M  1 loop /snap/core18/2745
loop2                       7:2    0  63.5M  1 loop /snap/core20/1891
loop3                       7:3    0  73.1M  1 loop /snap/core22/634
loop4                       7:4    0  73.9M  1 loop /snap/core22/750
loop5                       7:5    0 170.1M  1 loop /snap/lxd/24918
loop6                       7:6    0  61.9M  1 loop /snap/core20/1169
loop7                       7:7    0  53.3M  1 loop /snap/snapd/19361
loop8                       7:8    0  53.2M  1 loop /snap/snapd/19122
loop9                       7:9    0 166.4M  1 loop /snap/lxd/24846
sda                         8:0    0     8G  0 disk 
├─sda1                      8:1    0     1M  0 part 
├─sda2                      8:2    0   384M  0 part /boot
└─sda3                      8:3    0   7.6G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   7.5G  0 lvm  /

sudo mount /dev/sda1 ~/mountpoint

Sorry, user htb-student is not allowed to execute '/usr/bin/mount /dev/sda1 /home/htb-student/mountpoint' as root on ubuntu.

HTB{.*}

find / -path /proc -prune -o -type f -name HTB{.*} 2>/dev/null

grep -Rnw '/' -e 'HTB{.*}'

/lib/int-check.sh:1:HTB{1nt3rn4l_5cr1p7_l34k}
