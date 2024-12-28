# Enumerating & Retrieving Password Policies

## Enumerating the Password Policy - from Linux - SMB NULL Sessions

### With valid credentials 

With valid credentials, possible to obtain the password policy using crackmapexec :
crackmapexec smb $TARGET -u avazquez -p Password123 --pass-pol

### Without credentials

Without credentials, we may be able to obtain the password policy via an SMB NULL session or LDAP anonymous bind :

rpcclient -U "" -N $TARGET
rpcclient $> querydominfo
rpcclient $> getdompwinfo

enum4linux -P $TARGET

enum4linux-ng -P $TARGET -oA ilfreight
cat ilfreight.json
-> Additional features

## Enumerating Null Session - from Windows

- Establish a null session from windows
net use \\DC01\ipc$ "" /u:""
The command completed successfully.

Check errors when we try to connect :

a) Error: Account is Disabled
net use \\DC01\ipc$ "" /u:guest
System error 1331 has occurred.

This user can't sign in because this account is currently disabled.


b) Error: Password is Incorrect
net use \\DC01\ipc$ "password" /u:guest
System error 1326 has occurred.

The user name or password is incorrect.

c) Error: Account is locked out (Password Policy)
net use \\DC01\ipc$ "password" /u:guest
System error 1909 has occurred.

The referenced account is currently locked out and may not be logged on to.

## Enumerating the Password Policy - from Linux - LDAP Anonymous Bind

ldapsearch -h $TARGET -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength

## Enumerating the Password Policy - from Windows

CMD :

net accounts

POWERSHELL :

import-module .\PowerView.ps1
Get-DomainPolicy

## Default values when a domain is created

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


## LAB

ssh htb-student@$TARGET


ifconfig
ens224: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.225

for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
64 bytes from 172.16.5.5: icmp_seq=1 ttl=128 time=3.33 ms
64 bytes from 172.16.5.225: icmp_seq=1 ttl=64 time=0.059 ms

rpcclient -U "" -N 172.16.5.5
rpcclient $> getdompwinfo
min_password_length: 8
password_properties: 0x00000001
	DOMAIN_PASSWORD_COMPLEX
