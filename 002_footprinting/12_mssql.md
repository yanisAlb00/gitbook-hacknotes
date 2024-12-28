# MSSQL

Strong native support with .NET framework

SSMS : SQL Server Management Studio is a client-side application that can be used to manage MSSQL.

Examples of MSSQL clients :
/usr/bin/impacket-mssqlclient
/usr/share/doc/python3-impacket/examples/mssqlclient.py

Default databases :
master
model
msdb
tempdb
resource

the SQL service will likely run as NT SERVICE\MSSQLSERVER

encryption is not enforced by default

Windows authentication : Windows OS will process the request and use SAM database or Domain Controller

Port : TCP/1433

# Configuration


## Interesting parameters 

- MSSQL clients not using encryption to connect to the MSSQL server
- The use of self-signed certificates when encryption is being used. It is possible to spoof self-signed certificates
- The use of named pipes
- Weak & default sa credentials. Admins may forget to disable this account


# Footprinting the service


## Enumeration

locate mssqlclient

sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 $TARGET

msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248
msf6 auxiliary(scanner/mssql/mssql_ping) > run



## Interaction

python3 /usr/share/doc/python3-impacket/examples/mssqlclient.py backdoor@$TARGET -windows-auth

select name from sys.databases
SELECT HOST_NAME()
SELECT @@SERVERNAME
SELECT SERVERPROPERTY('InstanceName')
SELECT SERVERPROPERTY('ServerName')
xp_cmdshell(hostname)
