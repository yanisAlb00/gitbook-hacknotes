# 01 nmap enumeration services

Primarily, Nmap looks at the banners of the scanned ports and prints them out. If it cannot identify versions through the banners, Nmap attempts to identify them through a signature-based matching system. One disadvantage to Nmap's presented results is that the automatic scan can miss some information because sometimes Nmap does not know how to handle it.

Add --packet-trace option to increase verbosity

```
sudo nmap $TARGET -p- -sV -Pn -n --disable-arp-ping --packet-trace
```

NSOCK INFO \[198.9240s] nsock\_write(): Write request for 60 bytes to IOD #30 EID 723 \[10.129.206.76:31337] NSOCK INFO \[198.9240s] nsock\_read(): Read request from IOD #30 \[10.129.206.76:31337] (timeout: 5000ms) EID 730 NSOCK INFO \[198.9240s] nsock\_trace\_handler\_callback(): Callback: WRITE SUCCESS for EID 723 \[10.129.206.76:31337]

\--> Here Nmap cannot handle information from 31337 ports

Launch netcat connection :&#x20;

```
nc -nv $TARGET 31337
```

Intercept requests :&#x20;

```
sudo tcpdump -i tun0 host 10.10.15.217
sudo tcpdump -i tun0 host $TARGET
```
