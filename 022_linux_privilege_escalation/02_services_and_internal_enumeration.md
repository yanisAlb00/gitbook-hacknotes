# Linux Services & Internals Enumeration

## Internals

### Network Interfaces

ip a

### Hosts

cat /etc/hosts

### User's Last Login

lastlog

logger                                     **Never logged in**
shared                                     **Never logged in**
stacey.jenkins   pts/0    10.10.14.15      Tue Aug  2 18:29:15 +0000 2022
htb-student      pts/0    10.10.14.15      Wed Aug  3 13:37:22 +0000 2022  

### Logged In Users

w

 12:27:21 up 1 day, 16:55,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
cliff.mo pts/0    10.10.14.16      Tue19   40:54m  0.02s  0.02s -bash

### Command History

history

    1  id
    2  cd /home/cliff.moore
    3  exit
    4  touch backup.sh
    5  tail /var/log/apache2/error.log
    6  ssh ec2-user@dmz02.inlanefreight.local
    7  history

### Finding History Files

find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null

-rw------- 1 htb-student htb-student 387 Nov 27 14:02 /home/htb-student/.bash_history

### Cron

ls -la /etc/cron.daily/

total 48
drwxr-xr-x  2 root root 4096 Aug  2 17:36 .
drwxr-xr-x 96 root root 4096 Aug  2 19:34 ..
-rwxr-xr-x  1 root root  376 Dec  4  2019 apport
-rwxr-xr-x  1 root root 1478 Apr  9  2020 apt-compat
-rwxr-xr-x  1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x  1 root root 1187 Sep  5  2019 dpkg
-rwxr-xr-x  1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x  1 root root 1123 Feb 25  2020 man-db
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x  1 root root 4574 Jul 18  2019 popularity-contest
-rwxr-xr-x  1 root root  214 Apr  2  2020 update-notifier-common

### Proc

find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"

...SNIP...
startups/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitdroot@10.129.14.200sshroot@10.129.14.200sshd:
htb-student
[priv]sshd:
htb-student
[priv]/usr/bin/ssh-agent-D-a/run/user/1000/keyring/.ssh/usr/bin/ssh-agent-D-a/run/user/1000/keyring/.sshsshd:
htb-student@pts/2sshd:

--> The proc filesystem (proc / procfs) is a particular filesystem in Linux that contains information about system processes, hardware, and other system information. It is virtual but dynamically generated by the kernel.

## Services

### Installed Packages

apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list

### Sudo Version

sudo -V

### Binaries

ls -l /bin /usr/bin/ /usr/sbin/

### GTFObins

for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done

### Trace System Calls

strace ping -c1 10.129.112.20

execve("/usr/bin/ping", ["ping", "-c1", "10.129.112.20"], 0x7ffdc8b96cc0 /* 80 vars */) = 0
access("/etc/suid-debug", F_OK)         = -1 ENOENT (No such file or directory)
brk(NULL)                               = 0x56222584c000
arch_prctl(0x3001 /* ARCH_??? */, 0x7fffb0b2ea00) = -1 EINVAL (Invalid argument)
...SNIP...
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
...SNIP...
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libidn2.so.0", O_RDONLY|O_CLOEXEC) = 3
...SNIP...
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\237\2\0\0\0\0\0"..., 832) = 832
...

### Configuration Files

find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null

### Scripts

find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"

### Running Services by User

ps aux | grep root

...SNIP...
root           1  2.0  0.2 168196 11364 ?        Ss   12:31   0:01 /sbin/init splash
root         378  0.5  0.4  62648 17212 ?        S<s  12:31   0:00 /lib/systemd/systemd-journald
root         409  0.8  0.1  25208  7832 ?        Ss   12:31   0:00 /lib/systemd/systemd-udevd
root         457  0.0  0.0 150668   284 ?        Ssl  12:31   0:00 vmware-vmblock-fuse /run/vmblock-fuse -o rw,subtype=vmware-vmblock,default_permissions,allow_other,dev,suid
root         752  0.0  0.2  58780 10608 ?        Ss   12:31   0:00 /usr/bin/VGAuthService
root         755  0.0  0.1 248088  7448 ?        Ssl  12:31   0:00 /usr/bin/vmtoolsd

## LAB

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list

python-apt-common 2.0.0ubuntu0.20.04.6
python3-apport 2.20.11-0ubuntu27.20
python3-apt 2.0.0ubuntu0.20.04.6
python3-attr 19.3.0-2
python3-automat 0.8.0-1ubuntu1
python3-blinker 1.4+dfsg1-0.3ubuntu1
python3-certifi 2019.11.28-1
python3-cffi-backend 1.14.0-1build1
python3-chardet 3.0.4-4build1
python3-click 7.0-3
python3-colorama 0.4.3-1build1
python3-commandnotfound 20.04.4
python3-configobj 5.0.6-4
python3-constantly 15.1.0-1build1
python3-cryptography 2.8-3ubuntu0.1
python3-dbus 1.2.16-1build1
python3-debconf 1.5.73
python3-debian 0.1.36ubuntu1
python3-distro-info 0.23ubuntu1
python3-distro 1.4.0-1
python3-distupgrade 20.04.36
python3-distutils 3.8.10-0ubuntu1~20.04
python3-entrypoints 0.3-2ubuntu1
python3-gdbm 3.8.10-0ubuntu1~20.04
python3-gi 3.36.0-1
python3-hamcrest 1.9.0-3
python3-httplib2 0.14.0-1ubuntu1
python3-hyperlink 19.0.0-1
python3-idna 2.8-1
python3-incremental 16.10.1-3.2
python3-jwt 1.7.1-2ubuntu2
python3-keyring 18.0.1-2ubuntu1
python3-launchpadlib 1.10.13-1
python3-lazr.restfulclient 0.14.2-2build1
python3-lazr.uri 1.0.3-4build1
python3-lib2to3 3.8.10-0ubuntu1~20.04
python3-minimal 3.8.2-0ubuntu2
python3-nacl 1.3.0-5
python3-netifaces 0.10.4-1ubuntu4
python3-newt 0.52.21-4ubuntu2
python3-oauthlib 3.1.0-1ubuntu2
python3-openssl 19.0.0-1build1
python3-pexpect 4.6.0-1build1
python3-pkg-resources 45.2.0-1
python3-problem-report 2.20.11-0ubuntu27.20
python3-ptyprocess 0.6.0-1ubuntu1
python3-pyasn1-modules 0.2.1-0.2build1
python3-pyasn1 0.4.2-3build1
python3-pymacaroons 0.13.0-3
python3-requests-unixsocket 0.2.0-2
python3-requests 2.22.0-2ubuntu1
python3-secretstorage 2.3.1-2ubuntu1
python3-serial 3.4-5.1
python3-service-identity 18.1.0-5build1
python3-setuptools 45.2.0-1
python3-simplejson 3.16.0-2ubuntu2
python3-six 1.14.0-2
python3-software-properties 0.98.9.5
python3-systemd 234-3build2
python3-twisted-bin 18.9.0-11ubuntu0.20.04.2
python3-twisted 18.9.0-11ubuntu0.20.04.1
python3-update-manager 20.04.10.9
python3-urllib3 1.25.8-2ubuntu0.2
python3-wadllib 1.3.3-3build1
python3-yaml 5.3.1-1ubuntu0.1
python3-zope.interface 4.7.1-1
python3.11-minimal 3.11.3-1+focal1
python3.11 3.11.3-1+focal1
python3.8-minimal 3.8.10-0ubuntu1~20.04.7
python3.8 3.8.10-0ubuntu1~20.04.7
python3 3.8.2-0ubuntu2


--> Last version of Python : 3.11
