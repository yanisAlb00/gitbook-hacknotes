# Active Directory

## Access

xfreerdp /v:$TARGET /u:htb-student /p:Academy_student_AD!

rdesktop -u htb-student 10.129.164.117


In Scope For Assessment
Range/Domain	Description
INLANEFREIGHT.LOCAL	Customer domain to include AD and web services.
LOGISTICS.INLANEFREIGHT.LOCAL	Customer subdomain
FREIGHTLOGISTICS.LOCAL	Subsidiary company owned by Inlanefreight. External forest trust with INLANEFREIGHT.LOCAL
172.16.5.0/23	In-scope internal subnet.

## Password Spraying

It’s common to find a password policy that allows five bad attempts before locking out the account, with a 30-minute auto-unlock threshold. 

#!/bin/bash

for x in {{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}
    do echo $x;
done



