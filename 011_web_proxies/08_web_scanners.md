# Burp

1) Start scan on a specific request from Proxy History
2) Start a new scan on a set of targets
3) Start a scan on items in-scope

-> Crawler

-> Passive Scanner

### Active Scanner

1. It starts by running a Crawl and a web fuzzer (like dirbuster/ffuf) to identify all possible pages

2. It runs a Passive Scan on all identified pages

3. It checks each of the identified vulnerabilities from the Passive Scan and sends requests to verify them

4. It performs a JavaScript analysis to identify further potential vulnerabilities

5. It fuzzes various identified insertion points and parameters to look for common vulnerabilities like XSS, Command Injection, SQL Injection, and other common web vulnerabilities

--> Reporting

Target>Site map
Issue>Report issues for this host

# ZAP Scanner

### Spider
Similar to the Crawler feature in Burp

Locate a request from our History tab and select (Attack>Spider) from the right-click menu

OR

Use HUD and Spider Start

ZAP also has a different type of Spider called Ajax Spider

### Passive Scanner

ZAP Spider automatically runs its passive scanner on each response to see if it can identify potential issues from the source code

--> check the Alerts tab on the main ZAP UI 

### Active Scanner

HUD > Active Scan

High alerts are the ones that usually lead to directly compromising the web application or the back-end server

