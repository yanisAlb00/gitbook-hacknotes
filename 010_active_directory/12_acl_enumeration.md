# ACL Enumeration

## Enumerating ACLs with PowerView

PS C:\htb> Find-InterestingDomainAcl

### Target a specific user (wley) and find ACE manually

Import-Module .\PowerView.ps1
$sid = Convert-NameToSid wley
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}

ObjectAceType          : 00299570-246d-11d0-a768-00aa006e0529

Google :

https://learn.microsoft.com/en-us/windows/win32/adschema/r-user-force-change-password
-> Right to change user password 

OR

$guid= "00299570-246d-11d0-a768-00aa006e0529"
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl

Name              : User-Force-Change-Password
DisplayName       : Reset Password
DistinguishedName : CN=User-Force-Change-Password,CN=Extended-Rights,CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL
rightsGuid        : 00299570-246d-11d0-a768-00aa006e0529

### Using the -ResolveGUIDs Flag to find ACE automatically

$sid = Convert-NameToSid wley
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 

-> faster

### Example of attack

0. We have control over the user wley thanks to Responder and LLMNR NBT-NS Poisoning

1. Get list of AD USER
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

2. Display the rights of wley

foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}

Now have control over the user damundsen via the User-Force-Change-Password extended right of wley

3. Further Enumeration of Rights Using damundsen because wley could change its password

$sid2 = Convert-NameToSid damundsen
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2} -Verbose

AceType               : AccessAllowed
ObjectDN              : CN=Help Desk Level 1,OU=Security Groups,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : ListChildren, ReadProperty, GenericWrite

-> damundsen has GenericWrite privileges : he can add a member to the Help Desk Level 1

4. Investigating the Help Desk Level 1 Group with Get-DomainGroup

Get-DomainGroup -Identity "Help Desk Level 1" | select memberof

memberof                                                                      
--------                                                                      
CN=Information Technology,OU=Security Groups,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL

-> members of Help Desk Level 1 group get any rights provisioned to the Information Technology group

5. Investigating the Information Technology Group

$itgroupsid = Convert-NameToSid "Information Technology"
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose

AceType               : AccessAllowed
ObjectDN              : CN=Angela Dunn,OU=Server Admin,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : GenericAll

Information Technology group have GenericAll rights over the user adunn, which means we could:
- Modify group membership
- Force change a password
- Perform a targeted Kerberoasting attack and attempt to crack the user's password if it is weak

6. Looking for Interesting Access

$adunnsid = Convert-NameToSid adunn 
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $adunnsid} -Verbose

AceQualifier           : AccessAllowed
ObjectDN               : DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights  : ExtendedRight
ObjectAceType          : DS-Replication-Get-Changes-In-Filtered-Set
ObjectSID              : S-1-5-21-3842939050-3880317879-2865463114

-> this user can be leveraged to perform a DCSync attack

### Enumerating ACLs with BloodHound

.\SharpView.exe Get-DomainUser -Identity wley
transporter@4

.\SharpHound.exe -c All --zipfilename ILFREIGHT
transporter@4


sudo neo4j start
Starting Neo4j.
Started neo4j (pid:2694). It is available at http://localhost:7474

bloodhound
http://localhost:7474
neo4j
neo4j

## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET

forend

Import-Module .\PowerView.ps1
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

foreach($line in [System.IO.File]::ReadLines("C:\tools\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\forend'}}

Path                  : Microsoft.ActiveDirectory.Management.dll\ActiveDirectory:://RootDSE/CN=Dagmar
                        Payne,OU=HelpDesk,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : GenericAll
InheritanceType       : All
ObjectType            : 00000000-0000-0000-0000-000000000000
InheritedObjectType   : 00000000-0000-0000-0000-000000000000
ObjectFlags           : None
AccessControlType     : Allow
IdentityReference     : INLANEFREIGHT\forend
IsInherited           : False
InheritanceFlags      : ContainerInherit
PropagationFlags      : None

$sid = Convert-NameToSid forend
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid}

$sid = Convert-NameToSid forend
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}

$dpayne = Convert-NameToSid dpayne 
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $dpayne} -Verbose


domain:

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

forend

Node Info > Transitive Object Control

AddSelf

https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces

Self-Membership


