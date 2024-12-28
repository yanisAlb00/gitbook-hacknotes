# Active Infrastructure Enumeration

The webserver gives us a good idea of what operating system is running on the back-end server. For example IIS :

IIS 6.0: Windows Server 2003
IIS 7.0-8.5: Windows Server 2008 / Windows Server 2008R2
IIS 10.0 (v1607-v1709): Windows Server 2016
IIS 10.0 (v1809-): Windows Server 2019

In the case of Linux or BSD-based distributions, we can not be sure as they can run different web server versions in the case of Nginx or Apache.

Important to discover functionalities as URL rewriting functionality, load balancing, script engines used on the server, or an Intrusion detection system (IDS)

## HTTP Headers

curl -I "http://${TARGET}"

curl -I "http://${TARGET}" -H "Host: app.inlanefreight.local" 

    HTTP/1.1 200 OK
    Date: Thu, 23 Sep 2021 15:10:42 GMT
    Server: Apache/2.4.25 (Debian)
    X-Powered-By: PHP/7.3.5
    Link: <http://192.168.10.10/wp-json/>; rel="https://api.w.org/"
    Content-Type: text/html; charset=UTF-8

## Cookies

.NET: ASPSESSIONID<RANDOM>=<COOKIE_VALUE>
PHP: PHPSESSID=<COOKIE_VALUE>
JAVA: JSESSION=<COOKIE_VALUE>

## WhatWeb

whatweb -a3 https://www.facebook.com -v

    Detected Plugins:
    [ UncommonHeaders ]
    [ X-XSS-Protection ]
    HTTP Headers:
    HTTPServer -> ServerName

Wappalyzer has similar functionality to Whatweb, but the results are displayed while navigating the target URL.

## WafW00f

WafW00f is a web application firewall (WAF) fingerprinting tool that sends requests and analyses responses to determine if a security solution is in place. 

sudo apt install wafw00f -y
wafw00f -v https://www.tesla.com

## Aquatone

sudo apt install golang chromium-driver
go get github.com/michenriksen/aquatone
export PATH="$PATH":"$HOME/go/bin"

cat facebook_aquatone.txt | aquatone -out ./aquatone -screenshot-timeout 1000

aquatone_report.html

