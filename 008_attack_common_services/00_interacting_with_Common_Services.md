# Interacting with Common Services

## File Share Services

internal services : SMB, NFS, FTP, TFTP, SFTP
third-party cloud services : Dropbox, Google Drive, OneDrive, SharePoint, oAWS S3, Azure Blob Storage, or Google Cloud Storage

### Server Message Block (SMB)

Ways to interact :

[WINKEY] + [R]
\\192.168.220.129\Finance\

**CMD**

C:\htb> dir \\192.168.220.129\Finance\
C:\htb> net use n: \\192.168.220.129\Finance
C:\htb> net use n: \\192.168.220.129\Finance /user:plaintext Password123

-> find the number of files in the share
C:\htb> dir n: /a-d /s /b | find /c ":\"
29302

C:\htb>dir n:\*cred* /s /b
n:\Contracts\private\credentials.txt

C:\htb>dir n:\*secret* /s /b
n:\Contracts\private\secret.txt

c:\htb>findstr /s /i cred n:\*.*

**Powershell**

PS C:\htb> Get-ChildItem \\192.168.220.129\Finance\

    Directory: \\192.168.220.129\Finance

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2/23/2022   3:27 PM                Contracts

-> equivalent of net use in CMD
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

PS C:\htb> $username = 'plaintext'
PS C:\htb> $password = 'Password123'
PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
PS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred

-> find the number of files in the share
PS C:\htb> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count
29302

PS C:\htb> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
    Directory: N:\Contracts\private

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/23/2022   4:36 PM             25 credentials.txt

PS C:\htb> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
N:\Contracts\private\secret.txt:1:file with all credentials
N:\Contracts\private\credentials.txt:1:admin:SecureCredentials!

**Linux**

Linux (UNIX) machines can also be used to browse and mount SMB shares. 

Mount a CIFS drive :

sudo apt install cifs-utils
sudo mkdir /mnt/Finance
sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
OR
mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile

credentialfile :
username=plaintext
password=Password123
domain=.

find /mnt/Finance/ -name *cred*
/mnt/Finance/Contracts/private/credentials.txt

grep -rn /mnt/Finance/ -ie cred
/mnt/Finance/Contracts/private/credentials.txt:1:admin:SecureCredentials!
/mnt/Finance/Contracts/private/secret.txt:1:file with all credentials

### Email

Simple Mail Transfer Protocol (SMTP) : email delivery protocol used to send mail over the internet
POP3 and IMAP : protocol used to retrieve an email from a service

sudo apt-get install evolution
IF ERROR : export WEBKIT_FORCE_SANDBOX=0 && evolution.

### Databases

Different ways to interact with databases :

1.	Command Line Utilities (mysql or sqsh)

MSSQL :

sqsh -S $TARGET -U username -P Password123

Windows - SQLCMD :

C:\htb> sqlcmd -S $TARGET -U username -P Password123

MySQL :

mysql -u username -pPassword123 -h $TARGET
C:\htb> mysql.exe -u username -pPassword123 -h $TARGET


2.	A GUI application to interact with databases such as HeidiSQL, MySQL Workbench, or SQL Server Management Studio.

sudo dpkg -i dbeaver-<version>.deb
dbeaver &


3.	Programming Languages


# General Troubleshooting

Some reasons why we may not have access to a resource:

- Authentication
https://authress.io/knowledge-base/academy/topics/access-control-strategies
- Privileges
- Network Connection
- Firewall Rules
- Protocol Support