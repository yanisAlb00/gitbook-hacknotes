# Firewalls, IDS & IPS

Detect the presence of firewall :
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

Detect the presence of IDS/IPS :
Agressively scan one port from the same IP address and see what security measures are taken.

# Decoys

Decoys : Nmap generates various random IP addresses inserted into the IP header to disguise the origin of the packet sent.
RND:5 -> 5 IP Address generated randomly
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5

Testing Firewall Rules
sudo nmap 10.129.2.28 -n -Pn -p445 -O
sudo nmap 10.129.2.28 -n -Pn -p 445 -O -S 10.129.2.200 -e tun0

--> The option -S 10.129.2.200 allows us to specify a source IP address in the same subnet than the destination (10.129.2.28).

# DNS Proxying

In most cases, the traffic from the DNS server of the company is most trusted than those from the Internet.

Especially if we are in DMZ, it could be useful to use the DNS server as source to bypass security mechanisms.

Also, we can use DNS PORT (TCP/UDP 53) as a source-port for our scans in order to see our TCP packets trusted and passed through :

sudo nmap $TARGET -p80 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53

Now that we have found out that the firewall accepts TCP port 53, it is very likely that IDS/IPS filters might also be configured much weaker than others. We can test this by trying to connect to this port by using Netcat.

Connected to the filter port :
ncat -nv --source-port 53 $TARGET 50000
