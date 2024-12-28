# ColdFusion - Discovery & Enumeration

ColdFusion is a programming language and a web application development platform based on Java. 

It is used to build dynamic and interactive web applications that can be connected to various APIs and databases such as MySQL, Oracle, and Microsoft SQL Server. 

ColdFusion Markup Language (CFML) is the proprietary programming language used in ColdFusion to develop dynamic web applications.

--> Syntax similar to HTML

For instance, the cfquery tag can execute SQL statements to retrieve data from a database :

<cfquery name="myQuery" datasource="myDataSource">
  SELECT *
  FROM myTable
</cfquery>

cFloop to iterate through records retreived from database 

<cfloop query="myQuery">
  <p>#myQuery.firstName# #myQuery.lastName#</p>
</cfloop>

It is available for download from Adobe's website and can be installed on Windows, Mac, or Linux operating systems.

ColdFusion is still widely used by developers and organisations rather than for web developments

## Examples of vulnerabilities

CVE-2021-21087: Arbitrary disallow of uploading JSP source code
CVE-2020-24453: Active Directory integration misconfiguration
CVE-2020-24450: Command injection vulnerability
CVE-2020-24449: Arbitrary file reading vulnerability
CVE-2019-15909: Cross-Site Scripting (XSS) Vulnerability

ColdFusion exposes a fair few ports by default:

| Port Number | Protocol       | Description                                                                                                                                                            |
|-------------|----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 80          | HTTP           | Used for non-secure HTTP communication between the web server and web browser.                                                                                         |
| 443         | HTTPS          | Used for secure HTTP communication between the web server and web browser. Encrypts the communication between the web server and web browser.                          |
| 1935        | RPC            | Used for client-server communication. Remote Procedure Call (RPC) protocol allows a program to request information from another program on a different network device. |
| 25          | SMTP           | Simple Mail Transfer Protocol (SMTP) is used for sending email messages.                                                                                               |
| 8500        | SSL            | Used for server communication via Secure Socket Layer (SSL).                                                                                                           |
| 5500        | Server Monitor | Used for remote administration of the ColdFusion server.                                                                                                              |

--> Default ports can be changed during default installation

## Enumeration

- Port Scanning	
- File Extensions : ColdFusion pages typically use ".cfm" or ".cfc" file extensions
- HTTP Headers	: "Server: ColdFusion" or "X-Powered-By: ColdFusion"
- Error Messages
- Default Files	: "admin.cfm" or "CFIDE/administrator/index.cfm"

nmap -p- -sC -Pn 10.129.247.30 --open

PORT      STATE SERVICE
135/tcp   open  msrpc
8500/tcp  open  fmtp
49154/tcp open  unknown

Navigating to the IP:8500 lists 2 directories, CFIDE and cfdocs

/CFIDE/administrator : ColdFusion 8 Administrator login page

