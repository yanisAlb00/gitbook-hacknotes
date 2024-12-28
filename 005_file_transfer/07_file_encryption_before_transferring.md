# Protected File Transfers

## File Encryption on Windows

Import-Module .\Invoke-AESEncryption.ps1

Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

--> Create scan-results.txt.aes

## File Encryption on Linux


### Encrypting /etc/passwd with openssl

openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

### Decrypt passwd.enc with openssl


## Strong password

Using very strong and unique passwords for encryption for every company where a penetration test is performed.

