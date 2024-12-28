## Burp

Some of the paid-only features are:

Active web app scanner
Fast Burp Intruder
The ability to load certain Burp Extensions

https://portswigger.net/burp/releases/
java -jar </path/to/burpsuite.jar>

temporary project > use Burp Defaults > Start Burp

## OWASP Zed Attack Proxy (ZAP)
Opensource

https://www.zaproxy.org/download/

## Pre-Configured Browser

Burp    : Proxy>Intercept we can click on Open Browser
ZAP     : Firefox Icon

Firefox extension Foxy Proxy : 
127.0.0.1 as the IP, and 8080, Name : Burp/ZAP

Foxy Proxy icon and select Burp/ZAP

## Installing CA Certificate

1)

Burp :
http://burp
download the certificate from there by clicking on CA Certificate

OR

ZAP :
To get ZAP's certificate, we can go to (Tools>Options>Dynamic SSL Certificate), then click on Save:

2) 

Firefox :
about:preferences#privacy,
View Certificates
Import
select the downloaded CA certificate

Select :
Trust this CA to identify websites and Trust this CA to identify email users