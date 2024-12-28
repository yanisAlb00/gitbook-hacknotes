# Introduction to Web Attacks

## HTTP Verb Tampering

An HTTP Verb Tampering attack exploits web servers that accept many HTTP verbs and methods.

## Insecure Direct Object References (IDOR)

Suppose the web application lacks a robust access control mechanism and exposes direct references to files and resources. In that case, we may access other users' files and information by simply guessing or calculating their file IDs.

## XML External Entity (XXE) Injection

Send malicious XML data to disclose local files stored on the back-end server :
- Steal configuration files and passwords
- Steal the hosting server's credentials
- Steal source code of the web application : Whitebox Penetration Test

