# Windows Remote Management Protocols

WS-Management protocol : enable us to write applications that communicate remotely

## RDP

TCP/3389

Built-in encryption and handle SSL/TLS

### Footprinting the Service

#### Enumeration

"Remote Desktop" service is installed by default on Windows.

It can be activated using "Server Manager"

NLA (Network level authentication) -> determine if connections to the host are allowed.


nmap -sV -sC $TARGET -p3389 --script rdp*
nmap -sV -sC $TARGET -p3389 --packet-trace --disable-arp-ping -n
-> RDP Cookies : mstshash=nmap


sudo cpan
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
./rdp-sec-check.pl $TARGET

-> script named rdp-sec-check.pl identify the security settings of RDP servers

#### Interaction

xfreerdp /u:htb-student /p:"HTB_@cademy_student!" /v:$TARGET

## WinRM

The Windows Remote Management (WinRM) is a simple Windows integrated remote management protocol based on the command line.

WinRM uses the Simple Object Access Protocol (SOAP)

TCP/5985 and TCP/5986 for HTTPS

### Footprinting the Service

#### Enumeration

nmap -sV -sC $TARGET -p5985,5986 --disable-arp-ping -n

#### Interaction

evil-winrm -i $TARGET -u Cry0l1t3 -p P455w0rD!

## WMI

WMI allows read and write access to almost all settings on Windows systems.

WMI is typically accessed via PowerShell, VBScript, WMIC

The initialization of the WMI communication always takes place on TCP/135

/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@$TARGET "hostname"
