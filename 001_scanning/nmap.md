# Nmap

Ping all addresses of a target subnet :&#x20;

```bash
for i in {1..254} ;do (ping -c 1 192.168.207.$i | grep "bytes from" &) ;done 
64 bytes from 192.168.207.121: icmp_seq=1 ttl=125 time=48.5 ms 
64 bytes from 192.168.207.122: icmp_seq=1 ttl=61 time=48.0 ms 
64 bytes from 192.168.207.120: icmp_seq=1 ttl=61 time=49.1 ms
```

Build a list with every hosts which are alived :

```bash
sudo tee -a hosts.lst > /dev/null <<EOT 192.168.207.121 192.168.207.120 192.168.207.122 EOT
```

Default Nmap scan with versions :

```bash
nmap -sC -sV -oA nmap -iL hosts.lst --stats-every=5s
```

Common port scan

```bash
nmap -sS -p- $TARGET --stats-every=5s
```
