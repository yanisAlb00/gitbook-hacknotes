# Port Forwarding with Windows Netsh

Networking related tasks we can use Netsh for:

- Finding routes
- Viewing the firewall configuration
- Adding proxies
- Creating port forwarding rules

1) Using Netsh.exe to Port Forward

C:\Windows\system32> netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.15.150 connectport=3389 connectaddress=$TARGET_AFTER_PIVOT

2) Verifying Port Forward

C:\Windows\system32> netsh.exe interface portproxy show v4tov4

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
10.129.42.198   8080        172.16.5.25     3389

## LAB

xfreerdp /u:htb-student /p:"HTB_@cademy_student!" /v:$TARGET

netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.158.1 connectport=3389 connectaddress=172.16.5.19

netsh.exe interface portproxy show v4tov4

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
10.129.158.1    8080        172.16.5.19     3389

xfreerdp /u:victor /p:"pass@123" /v:$TARGET:8080

