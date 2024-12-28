# Hyper-V Administrators

If Domain Controllers have been virtualized, then the virtualization admins should be considered Domain Admins. 

They could easily create a clone of the live Domain Controller and mount the virtual disk offline to obtain the NTDS.dit file and extract NTLM password hashes for all users in the domain.

https://www.tenable.com/cve/CVE-2018-0952
https://www.tenable.com/cve/CVE-2019-0841

--> SYSTEM privileges

## Target File

https://raw.githubusercontent.com/decoder-it/Hyper-V-admin-EOP/master/hyperv-eop.ps1

takeown /F C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe

sc.exe start MozillaMaintenance

