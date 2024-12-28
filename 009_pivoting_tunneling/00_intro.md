# Definition

Different terms to define a pivot :
Pivot Host
Proxy
Foothold
Beach Head system
Jump Host

Tunneling : Encapsulation (subset of pivoting)
-> avoid detection

# Networking

Every computer that is communicating on a network needs an IP address. If it doesn't have one, it is not on a network. 

Often DHCP use

Static IP assignment is common with:

Servers
Routers
Switch virtual interfaces
Printers
And any devices that are providing critical services to the network

NIC = Network Interface Card

Linux :
ifconfig
Windows :
ipconfig

Default gateway : network traffic is destined for an IP address out of subnet range

## Routing

Consult routing table :
netstat -r

Pwnbox is not using any routing protocols (EIGRP, OSPF, BGP, etc...) to learn each of those routes.

Default route : Any traffic destined for networks not present in the routing table

## Protocols, Services & Ports

Logical ports aren't physical things we can touch or plug anything into.
-> software assigned to applications

--> use Draw.io to build a visual of the network


