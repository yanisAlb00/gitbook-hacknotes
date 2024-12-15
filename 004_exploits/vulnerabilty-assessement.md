# Vulnerabilty Assessement

## Nmap NSE Scripts :

Identify scripts which are exploits :

```
grep Exploits /usr/share/nmap/scripts/*.nse
```

Get more info on nmap script :&#x20;

```
nmap --script-help=clamav-exec.nse
```

HTTPS :

```bash
sudo nmap -sV -p 443 --script "vuln" 192.168.50.124 --stats-every=5s
```

HTTP :&#x20;

```bash
sudo nmap -sV -p 80 --script "vuln" 192.168.50.124 --stats-every=5s
```

## Public exploits

a

[https://www.exploit-db.com/](https://www.exploit-db.com/)

[https://packetstormsecurity.com/](https://packetstormsecurity.com/)

[https://github.com/](https://github.com/)&#x20;

```
firefox --search "Microsoft Edge site:exploit-db.com"
```

```
sudo apt update && sudo apt install exploitdb
searchsploit
searchsploit remote smb microsoft windows
searchsploit -m windows/remote/48537.py
```

