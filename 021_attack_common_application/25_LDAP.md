# LDAP

LDAP (Lightweight Directory Access Protocol) is a protocol used to access and manage directory information. 

LDAP does not encrypt its traffic by default
Vulnerable to LDAP injection attacks

2 popular implementations :
- OpenLDAP
- Microsoft Active Directory

LDAP supports various requests, such as bind, unbind, search, compare, add, delete, modify, etc

## Components of requests/responses

An LDAP request is comprised of several components:

1. Session connection: The client connects to the server via an LDAP port (usually 389 or 636).
2. Request type: The client specifies the operation it wants to perform, such as bind, search, etc.
3. Request parameters: The client provides additional information for the request, such as the distinguished name (DN) of the entry to be accessed or modified, the scope and filter of the search query, the attributes and values to be added or changed, etc.
4. Request ID: The client assigns a unique identifier for each request to match it with the corresponding response from the server.

The server sends back a response message that includes several components :

1. Response type: The server indicates the operation that was performed in response to the request.
2. Result code: The server indicates whether or not the operation was successful and why.
3. Matched DN: If applicable, the server returns the DN of the closest existing entry that matches the request.
4. Referral: The server returns a URL of another server that may have more information about the request, if applicable.
5. Response data: The server returns any additional data related to the response, such as the attributes and values of an entry that was searched or modified.

After receiving and processing the response, the client disconnects from the LDAP port.

## ldapsearch

ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"


dn: uid=jdoe,ou=people,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: John Doe
sn: Doe
uid: jdoe
mail: john.doe@example.com

result: 0 Success

## LDAP Injection

Special characters that can change the request meaning :
*
( )	
|
&
(cn=*)

For example, suppose an application uses the following LDAP query to authenticate users:

(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))


--> Inject * into password : gain access with any username

## Enumeration

nmap -p- -sC -sV --open --min-rate=1000 10.129.204.229

PORT    STATE SERVICE VERSION
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Login
389/tcp open  ldap    OpenLDAP 2.2.X - 2.3.X

## LAB

http://10.129.205.18:80

Username : *
Password : *

