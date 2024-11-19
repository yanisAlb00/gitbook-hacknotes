# Pass The Hash (Pth)

## Requisites

* SMB port (445) open
* Windows File and Printer Sharing feature to be enabled
* admin share called **ADMIN$** to be available
* Valid credentials with local administrative permissions

All are common on enterprise network

## How does Pth works

* Open a connection using SMB Protocol
* Start a service on remote host (usually cmd.exe or powershell)
* Communicate with in using Named Pipes

Pth uses Service Control Manager API

Pth is used against services that use NTLM as authentication (it consists to send NTLM Hash instead of cleartext password). Pth is not possible against services that use Kerberos Authentication.

## From Linux with wmiexec (from Impacket)

```
wmiexec.py -hashes :2892D26CDF84D7A70E2EB3B9F05C425E Administrator@192.168.229.72

```



