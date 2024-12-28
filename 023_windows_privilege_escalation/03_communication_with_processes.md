# Communication with Processes

## Access Tokens

https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens

## Enumerating Network Services

### Display Active Network Connections

```
netstat -ano

Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:21             0.0.0.0:0              LISTENING       3812
  TCP    0.0.0.0:80             0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       836
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
```

The main thing to look for with Active Network Connections are entries listening on loopback addresses (127.0.0.1 and ::1) 

--> Often less secure because they are not accessible to the network

## Named Pipes

Pipes are essentially files stored in memory that get cleared out after being read. Cobalt Strike uses Named Pipes for every command (excluding BOF). Essentially the workflow looks like this:

1. Beacon starts a named pipe of \.\pipe\msagent_12
2. Beacon starts a new process and injects command into that process directing output to \.\pipe\msagent_12
3. Server displays what was written into \.\pipe\msagent_12

### Listing Named Pipes with Pipelist

```
pipelist.exe /accepteula

PipeList v1.02 - Lists open named pipes
Copyright (C) 2005-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

Pipe Name                                    Instances       Max Instances
---------                                    ---------       -------------
InitShutdown                                      3               -1
lsass                                             4               -1
ntsvcs                                            3               -1
scerpc                                            3               -1
Winsock2\CatalogChangeListener-340-0              1                1
Winsock2\CatalogChangeListener-414-0              1                1
epmapper                                          3               -1
Winsock2\CatalogChangeListener-3ec-0              1                1
Winsock2\CatalogChangeListener-44c-0              1                1
LSM_API_service                                   3               -1
atsvc                                             3               -1
Winsock2\CatalogChangeListener-5e0-0              1                1
eventlog                                          3               -1
Winsock2\CatalogChangeListener-6a8-0              1                1
spoolss                                           3               -1
Winsock2\CatalogChangeListener-ec0-0              1                1
wkssvc                                            4               -1
trkwks                                            3               -1
vmware-usbarbpipe                                 5               -1
srvsvc                                            4               -1
ROUTER                                            3               -1
vmware-authdpipe                                  1                1
```

### Listing Named Pipes with PowerShell

```
gci \\.\pipe\


    Directory: \\.\pipe


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              3 InitShutdown
------       12/31/1600   4:00 PM              4 lsass
------       12/31/1600   4:00 PM              3 ntsvcs
------       12/31/1600   4:00 PM              3 scerpc


    Directory: \\.\pipe\Winsock2


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              1 Winsock2\CatalogChangeListener-34c-0


    Directory: \\.\pipe


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
------       12/31/1600   4:00 PM              3 epmapper
```

### Reviewing LSASS Named Pipe Permissions

```
accesschk.exe /accepteula \\.\Pipe\lsass -v

Accesschk v6.12 - Reports effective permissions for securable objects
Copyright (C) 2006-2017 Mark Russinovich
Sysinternals - www.sysinternals.com

\\.\Pipe\lsass
  Untrusted Mandatory Level [No-Write-Up]
  RW Everyone
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW NT AUTHORITY\ANONYMOUS LOGON
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW APPLICATION PACKAGE AUTHORITY\Your Windows credentials
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL
  RW BUILTIN\Administrators
        FILE_ALL_ACCESS
```

--> Only administrators have full access to the LSASS process

## Named Pipes Attack Example

Example :
https://www.exploit-db.com/exploits/48021

### Search for all named pipes that allow write access

```
accesschk.exe -w \pipe\* -v
```

Checking WindscribeService Named Pipe Permissions :

```
accesschk.exe -accepteula -w \pipe\WindscribeService -v

Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com

\\.\Pipe\WindscribeService
  Medium Mandatory Level (Default) [No-Write-Up]
  RW Everyone
        FILE_ALL_ACCESS
```

-->  Everyone group does indeed have FILE_ALL_ACCESS (All possible access rights) over the pipe

## LAB

xfreerdp /v:10.129.43.43 /u:htb-student /p:HTB_@cademy_stdnt!

netstat -ano

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:21             0.0.0.0:0              LISTENING       2144

tasklist /svc /FI "PID eq 2144"

Image Name                     PID Services
========================= ======== ============================================
FileZilla Server.exe          2144 FileZilla Server

cd C:\Tools\

Pipelist\pipelist64.exe /accepteula

SQLLocal\SQLEXPRESS01                             3               -1
MSSQL$SQLEXPRESS01\sql\query                      2               -1

accesschk64.exe -w \pipe\* -v > accesschk.txt

accesschk64.exe -accepteula -w \pipe\WindscribeService -v

accesschk64.exe -accepteula -w \pipe\SQLLocal\SQLEXPRESS01 -v

Accesschk v6.13 - Reports effective permissions for securable objects
Copyright ⌐ 2006-2020 Mark Russinovich
Sysinternals - www.sysinternals.com

\\.\Pipe\SQLLocal\SQLEXPRESS01
  Medium Mandatory Level (Default) [No-Write-Up]
  RW NT SERVICE\MSSQL$SQLEXPRESS01
        FILE_CREATE_PIPE_INSTANCE
        FILE_APPEND_DATA
        READ_CONTROL
        WRITE_DAC
  RW Everyone
        FILE_ADD_FILE
        FILE_LIST_DIRECTORY
        FILE_READ_ATTRIBUTES
        FILE_READ_DATA
        FILE_READ_EA
        FILE_WRITE_ATTRIBUTES
        FILE_WRITE_DATA
        FILE_WRITE_EA
        SYNCHRONIZE
        READ_CONTROL

accesschk64.exe /accepteula \\.\Pipe\SQLLocal\SQLEXPRESS01 -v

\\.\Pipe\SQLLocal\SQLEXPRESS01
  Error getting security:
  All pipe instances are busy.
No matching objects found.

accesschk64.exe -accepteula -w \pipe\SQLLocal\SQLEXPRESS01 -v

--> Account name : NT SERVICE\MSSQL$SQLEXPRESS01