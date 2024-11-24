# DNS

Get the IP address associated to a domain name :

```bash
host www.megacorpone.com
```

By default, host looks for a A record. It is possible to specify record type :&#x20;

```bash
host -t mx megacorpone.com
host -t txt megacorpone.com
```

Bruteforcing subdomains :&#x20;

```bash
for ip in $(cat list.txt); do host $ip.medtech.com; done

www.medtech.com has address 208.91.197.27
ftp.medtech.com has address 208.91.197.27
mail.medtech.com is an alias for mail.medtech.com.netsolmail.net.
mail.medtech.com.netsolmail.net is an alias for http.netsol.xion.oxcs.net.
http.netsol.xion.oxcs.net has address 23.81.68.43
owa.medtech.com has address 208.91.197.27
proxy.medtech.com has address 208.91.197.27
router.medtech.com has address 208.91.197.27
```

OR

```bash
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

Reverse DNS lookup :

```bash
for ip in $(seq 200 254); do host 51.222.169.$ip; done | grep -v "not found"
208.169.222.51.in-addr.arpa domain name pointer admin.megacorpone.com.
209.169.222.51.in-addr.arpa domain name pointer beta.megacorpone.com.
```

Standard DNS Scan :&#x20;

```bash
dnsrecon -d www.megacorpone.com -t std
[*] std: Performing General Enumeration against: www.megacorpone.com...
[-] DNSSEC is not configured for www.megacorpone.com
[*] 	 A www.megacorpone.com 149.56.244.87
[*] Enumerating SRV Records
[+] 0 Records Found
```

Global enumeration :&#x20;

```bash
dnsenum megacorpone.com
```

From Windows :&#x20;

```bash
nslookup mail.megacorptwo.com

nslookup -type=TXT info.megacorptwo.com 192.168.50.151
```
