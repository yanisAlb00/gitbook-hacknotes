# Windows Privileges Overview

Privileges are different from access rights : 
- Acces rights : system rights are used to grant or deny access to securable objects. 
- User and group privileges are stored in a database and granted via an access token when a user logs on to a system.

## Windows Authorization Process

Every single security principal is identified by a unique Security Identifier (SID)

Subject try to access an object :

- User's access token from subject (including their user SID, SIDs for any groups they are members of, privilege list, and other access information) is compared against Access Control Entries (ACEs) within the object's security descriptor 
- Once this comparison is complete, a decision is made to either grant or deny access.

## Rights and Privileges in Windows

Default Administrators : Domain Admins and Enterprise Admins are "super" groups.

Server Operators : Members can modify services, access SMB shares, and backup files.

Backup Operators : Members are allowed to log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, read the registry remotely, and access the file system on the DC via SMB. This group is sometimes added to the local Backup Operators group on non-DCs.

Print Operators : Members can log on to DCs locally and "trick" Windows into loading a malicious driver.
Hyper-V Administrators	If there are virtual DCs, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.

Account Operators : Members can modify non-protected accounts and groups in the domain.

Remote Desktop Users : Members are not given any useful permissions by default but are often granted additional rights such as Allow Login Through Remote Desktop Services and can move laterally using the RDP protocol.

Remote Management Users : Members can log on to DCs with PSRemoting (This group is sometimes added to the local remote management group on non-DCs).

Group Policy Creator Owners : Members can create new GPOs but would need to be delegated additional permissions to link GPOs to a container such as a domain or OU.

Schema Admins : Members can modify the Active Directory schema structure and backdoor any to-be-created Group/GPO by adding a compromised account to the default object ACL.

DNS Admins : Members can load a DLL on a DC, but do not have the necessary permissions to restart the DNS server. They can load a malicious DLL and wait for a reboot as a persistence mechanism. Loading a DLL will often result in the service crashing. A more reliable way to exploit this group is to create a WPAD record.


## User Rights Assignment

Example :

Setting Constant			
SeNetworkLogonRight	

Setting Name
Access this computer from the network	

Standard Assignment
Administrators, Authenticated Users	

Description
Determines which users can connect to the device from the network. This is required by network protocols such as SMB, NetBIOS, CIFS, and COM+.

### Local Admin User Rights - Elevated


```
whoami 

winlpe-srv01\administrator
```

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== ========
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Disabled
SeSecurityPrivilege                       Manage auditing and security log                                   Disabled

```

### Standard User Rights

```
whoami 

winlpe-srv01\htb-student
```

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

### Backup Operators Rights

```
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```