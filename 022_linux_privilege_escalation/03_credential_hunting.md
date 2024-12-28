# Credential Hunting

Example is MySQL database credentials within WordPress configuration files :

cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'

define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'WPadmin123!' );

## Find config files

find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null

/etc/ssh/ssh_config
/etc/ssh/sshd_config
/etc/python3/debian_config
/etc/kbd/config
/etc/manpath.config
/boot/config-4.4.0-116-generic
/boot/grub/i386-pc/configfile.mod
/sys/devices/pci0000:00/0000:00:00.0/config
/sys/devices/pci0000:00/0000:00:01.0/config

## Find SSH Keys

ls ~/.ssh

id_rsa  id_rsa.pub  known_hosts

--> known_hosts : past host connected by ssh key (useful for lateral movement)

## LAB

ssh htb-student@$TARGET
Academy_LLPE!

find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null

find / ! -path "*/proc/*" -iname "*wp-config*" -type f 2>/dev/null
/var/www/html/wp-config.php
/var/www/html/wp-config-sample.php

cat /var/www/html/wp-config.php | grep 'DB_USER\|DB_PASSWORD'
define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'W0rdpr3ss_sekur1ty!' );
