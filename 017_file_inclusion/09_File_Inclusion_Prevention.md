# File Inclusion Prevention

## File Inclusion Prevention

--> Keep aware of the privileges each function has and ensure that no user input is directly going into them (consider any function that can read files and not only function which can be include files)

--> Avoid passing any user-controlled inputs into any file inclusion functions or APIs

--> Utilize a limited whitelist of allowed user inputs

--> Create a whitelist that contains all existing paths used in the front-end

## Preventing Directory Traversal

Path Transversal allow attackers to :
- Read /etc/passwd and potentially find SSH Keys or know valid user names for a password spray attack
- Find other services on the box such as Tomcat and read the tomcat-users.xml file
- Discover valid PHP Session Cookies and perform session hijacking
- Read current web application configuration and source code

--> PHP has basename(), which will read the path and only return the filename portion

--> Sanitize the user input to recursively remove any attempts of traversing directories

while(substr_count($input, '../', 0)) {
    $input = str_replace('../', '', $input);
};

## Web Server Configuration

--> Set to off these parameters :
allow_url_fopen
allow_url_include

--> Run the application within Docker : it will lock web applications to their web root directory

Or add :

open_basedir = /var/www in the php.ini file

## Web Application Firewall (WAF)

--> Utilize a Web Application Firewall (WAF), such as ModSecurity

--> ModSecurity minimizes false positives by offering a permissive mode

Just having a WAF in permissive mode can be an early warning sign that your application is being attacked (in case of zero-day for examples)

## LAB

ssh htb-student@$TARGET
HTB_@cademy_stdnt!

find / -name *php.ini* -ls 2>/dev/null

   140927     72 -rw-r--r--   1 root     root        72539 Oct  6  2020 /etc/php/7.4/cli/php.ini
   140928     72 -rw-r--r--   1 root     root        72941 Oct  6  2020 /etc/php/7.4/apache2/php.ini
    36923     72 -rw-r--r--   1 root     root        72539 Oct  6  2020 /usr/lib/php/7.4/php.ini-production.cli
    36922     72 -rw-r--r--   1 root     root        72941 Oct  6  2020 /usr/lib/php/7.4/php.ini-production
    36921     72 -rw-r--r--   1 root     root        72890 Oct  6  2020 /usr/lib/php/7.4/php.ini-development

sudo vi /etc/php/7.4/apache2/php.ini
HTB_@cademy_stdnt!


/basename
n

/filter.default

 976 [filter]
 977 ; http://php.net/filter.default
 978 ;filter.default = unsafe_raw
 979 
 980 ; http://php.net/filter.default-flags
 981 ;filter.default_flags =

-->  978 filter.default = unsafe_raw

sudo /etc/init.d/apache2 restart
Restarting apache2 (via systemctl): apache2.service.

sudo echo "<?php system($_GET['cmd']); ?>" > /var/www/html/shell.php
-bash: /var/www/html/shell.php: Permission denied

htb-student@lfi-harden:/var/www/html$ sudo vi shell.php
<?php system($_GET['cmd']); ?>

http://10.129.21.85/shell.php

tail -f /var/log/apache2/error.log

058] PHP Warning:  system(): Cannot execute a blank command in /var/www/html/shell.php on line 1

http://10.129.21.85/shell.php?cmd=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

sudo vi /etc/php/7.4/apache2/php.ini
filter.default = PHP_INI_PERDIR

sudo /etc/init.d/apache2 restart

http://10.129.21.85/shell.php?cmd=id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

sudo vi /etc/php/7.4/apache2/php.ini

--> add system in disable_functions

disable_functions = pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wifcontinued,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_get_handler,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority,pcntl_async_signals,pcntl_unshare,system

http://10.129.21.85/shell.php?cmd=id

[Mon Jan 01 20:42:31.811428 2024] [php7:warn] [pid 2370] [client 10.10.15.212:56702] PHP Warning:  system() has been disabled for security reasons in /var/www/html/shell.php on line 1
