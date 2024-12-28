# Introduction to Linux Privilege Escalation

## Enumeration

https://github.com/rebootuser/LinEnum

- OS Version
- Kernel Version
- Running Services (especially those running as root)

List Current Processes
ps aux | grep root

- Installed Packages and Versions
- Logged in Users

List Current Processes
ps au

- User Home Directories

ls /home

User's Home Directory Contents
ls -la /home/stacey.jenkins/

cat .bash_history

- SSH Directory Contents

ls -l ~/.ssh

- Bash History
history

- Sudo Privileges

sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump

- Configuration Files

- Readable Shadow File

- Password Hashes in /etc/passwd

cat /etc/passwd

- Cron Jobs
ls -la /etc/cron.daily/

- File Systems & Additional Drives

lsblk

- SETUID and SETGID Permissions

- Find Writable Directories

find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null

- Writeable Files

find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

