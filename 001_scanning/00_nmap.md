nmap -sC -sV -oA nmap $TARGET --stats-every=5s
nmap -sS -p- $TARGET --stats-every=5s


ping and check TTL : https://subinsb.com/default-device-ttl-values/

sudo nmap -v -O $TARGET

"If our target sends an SYN-ACK flagged packet back to the scanned port, Nmap detects that the port is open.
If the packet receives an RST flag, it is an indicator that the port is closed.
If Nmap does not receive a packet back, it will display it as filtered.

Nmap's TCP ACK scan : -sA -> much harder to filter for firewall
Nmap's TCP SYN scan : -sS


Target network range : 10.129.2.0/24
nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

-sn : Disables port scanning. If we disable port scan (-sn), Nmap automatically ping scan with ICMP Echo Requests.
-oA tnet : Stores the results in all formats starting with the name 'tnet'.
cat hosts.lst
nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

-iL	Performs defined scans against targets in provided 'hosts.lst' list.

nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f5
nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f5
nmap $TARGET -sn -oA host -PE --packet-trace --reason

-PE : Performs the ping scan by using 'ICMP Echo requests' against the target.
--packet-trace : Shows all packets sent and received
--reason : Displays the reason for specific result.

TTL gives OS type : https://subinsb.com/default-device-ttl-values/

open : This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams as well as SCTP associations.
closed : When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an RST flag. This scanning method can also be used to determine if our target is alive or not.
filtered : Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.
unfiltered : This state of a port only occurs during the TCP-ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed.
open|filtered : If we do not get a response for a specific port, Nmap will set it to that state. This indicates that a firewall or packet filter may protect the port.
closed|filtered : This state only occurs in the IP ID idle scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.

By, default : Top 1000 ports and -sS (SYN Scan) if Root or -sT (TCP Scan) otherwise

one by one : -p 22,25,80,139,445
range : -p 22-445
top ports : --top-ports=10
top 100 ports : -F

Connected scan : The Nmap TCP Connect Scan (-sT) uses the TCP three-way handshake
the Connect scan does not leave any unfinished connections -> the most stealthy (less likely to be detected)
Connect scan is slower

sudo nmap $TARGET -p 139 --packet-trace -n --disable-arp-ping -Pn
-n : Disables DNS resolution.
--disable-arp-ping : Disables ARP ping.
-Pn : Disables ICMP Echo requests.

dropped (no response) --> scanned in 2.06 seconds
filtered (rejected) --> scanned in 0.05 seconds

UDP scan (-sU) much slower than the TCP scan (-sS)
Port unreachable (type=3/code=3) : we know that the port is indeed closed

-sV : Performs a service scan

sudo nmap $TARGET -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sV

sudo nmap 10.129.2.49 -p- -oX target
-p- : all ports
-oX save in .xml
xsltproc target.xml -o target.html : generate html
firefox target.html

sudo nmap 10.129.2.49 -p- -sV --stats-every=5s
--stats-every=5s : show the progress of the scan every 5 seconds

sudo nmap 10.129.2.49 -p- -sV --stats-every=5s -v
-v : increase verbosity

