# PowerShell Session File Transfer

PowerShell Remoting (WinRM) allows us to execute scripts or commands on a remote computer using PowerShell sessions.

--> Useful when HTTP, HTTPS, or SMB are unavailable.

Default ports 
TCP/5985 : HTTP
TCP/5986 : HTTPS

1. Confirm WinRM port TCP 5985 is Open

whoami
    htb\administrator

Test-NetConnection -ComputerName DATABASE -Port 5985

2. Create a PowerShell Remoting Session to DATABASE

$Session = New-PSSession -ComputerName DATABASE


3. Copy samplefile.txt from our Localhost to the DATABASE Session

Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

4. Copy DATABASE.txt from DATABASE01 Session to our Localhost

Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session

# RDP

Mounting a Linux Folder Using rdesktop

rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'

Mounting a Linux Folder Using xfreerdp

xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer

To access the directory, we can connect to \\tsclient\

Alternatively, from Windows, the native mstsc.exe remote desktop client can be used.

