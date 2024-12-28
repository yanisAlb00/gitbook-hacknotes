# Splunk - Discovery & Enumeration

Splunk is a log analytics tool used to gather, analyze and visualize data. Though not originally intended to be a SIEM tool, Splunk is often used for security monitoring and business analytics. 

Historically :
- information disclosure vulnerability (CVE-2018-11409) 
- authenticated remote code execution vulnerability in very old versions (CVE-2011-4642)

## Discovery/Footprinting

The Splunk web server runs by default on port 8000. On older versions of Splunk, the default credentials are admin:changeme

Also check for common weak passwords such as admin, Welcome, Welcome1, Password123

sudo nmap -sV 10.129.201.50

8000/tcp open  ssl/http      Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd

## Enumeration

Splunk Enterprise trial converts to a free version after 60 days, which doesn’t require authentication

Once logged in to Splunk (or having accessed an instance of Splunk Free), we can browse data, run reports, create dashboards, install applications from the Splunkbase library, and install custom applications.

https://10.129.201.50:8000/en-US/app/launcher/home

## LAB

sudo nmap -sV 10.129.201.50

https://10.129.201.50:8000/en-GB/app/launcher/home

Help > About

Splunk
Version:
8.2.2
Build:
87344edfcdb4
Server:
APP03
Third-Party Software Credits and Attributions