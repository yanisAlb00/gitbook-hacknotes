# Situational Awareness

## Network Information

### Interface(s), IP Address(es), DNS Information

```
ipconfig /all
```

### ARP Table

```
arp -a
```

### Routing Table

```
route print
```

## Enumerating Protections

### Check Windows Defender Status

```
Get-MpComputerStatus

AMServiceEnabled                : True
AntispywareEnabled              : True
AntivirusEnabled                : True
BehaviorMonitorEnabled          : False
IoavProtectionEnabled           : False
OnAccessProtectionEnabled       : False
RealTimeProtectionEnabled       : False

```

### List AppLocker Rules

```
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

PublisherConditions : {*\*\*,0.0.0.0-*}
PublisherExceptions : {}
PathExceptions      : {}
HashExceptions      : {}
Id                  : a9e18c21-ff8f-43cf-b9fc-db40eed693ba
Name                : (Default Rule) All signed packaged apps
Description         : Allows members of the Everyone group to run packaged apps that are signed.
UserOrGroupSid      : S-1-1-0
Action              : Allow
...
```

### Test AppLocker Policy

```
Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone

FilePath                    PolicyDecision MatchingRule
--------                    -------------- ------------
C:\Windows\System32\cmd.exe         Denied c:\windows\system32\cmd.exe
```

## LAB

exegol start htb_desk --vpn windows-privesc.ovpn --desktop
xfreerdp /v:10.129.6.161 /u:htb-student /p:HTB_@cademy_stdnt!

ipconfig /all


Ethernet adapter Ethernet1:

   Connection-specific DNS Suffix  . :
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter
   Physical Address. . . . . . . . . : 00-50-56-B9-06-D2
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::4091:7ace:de7b:5326%2(Preferred)
   IPv4 Address. . . . . . . . . . . : 172.16.20.45(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.254.0
   Default Gateway . . . . . . . . . : 172.16.20.1
   DHCPv6 IAID . . . . . . . . . . . : 151015510
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2D-49-44-D0-00-50-56-B9-06-D2
   DNS Servers . . . . . . . . . . . : 8.8.8.8
   NetBIOS over Tcpip. . . . . . . . : Enabled

Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

PathConditions      : {%SYSTEM32%\WindowsPowerShell\v1.0\powershell_ise.exe}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 684d8b3e-7656-4451-8abe-2588d772db8f
Name                : Block PowerShell ISE
Description         :
UserOrGroupSid      : S-1-1-0
Action              : Deny

PathConditions      : {%PROGRAMFILES%\*}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 921cc481-6e17-4653-8f75-050b80acca20
Name                : (Default Rule) All files located in the Program Files folder
Description         : Allows members of the Everyone group to run applications that are
                      folder.
UserOrGroupSid      : S-1-1-0
Action              : Allow

PathConditions      : {c:\windows\system32\cmd.exe}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 9b8293c1-49c7-4bbb-aa17-52c4232b1fe4
Name                : c:\windows\system32\cmd.exe
Description         :
UserOrGroupSid      : S-1-1-0
Action              : Deny