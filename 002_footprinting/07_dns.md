# DNS

DNS is a system for resolving computer names into IP addresses.

There are several types of DNS servers that are used worldwide:

- DNS root server : The root servers of the DNS are responsible for the top-level domains (TLD). (ICANN) coordinates the work of the root name servers. There are 13 such root servers around the globe.
- Authoritative name server
- Non-authoritative name server
- Caching server
- Forwarding server
- Resolver 

DNS is mainly unencrypted. Devices on the local WLAN and Internet providers can therefore hack in and spy on DNS queries.

It is possible to use DNS over TLS (DoT) or DNS over HTTPS (DoH) or the protocol NSCrypt

Different DNS records are used for the DNS queries :

A       Returns an IPv4 address of the requested domain as a result.
AAAA	Returns an IPv6 address of the requested domain.
MX	    Returns the responsible mail servers as a result.
NS	    Returns the DNS servers (nameservers) of the domain.
TXT	    This record can contain various information (Certificate, SPF, DMARC, ...)
CNAME	This record serves as an alias if 2 domains are supposed to point to the same IP.
PTR	    PTR record works the other way around (reverse lookup). Convert IP address to valid domain.
SOA	    Provides information about DNS zone and email address of the administrative contact.

dig soa www.inlanefreight.com

The dot (.) is replaced by an at sign (@) in the email address.

# Configuration

1. local DNS configuration files
    named.conf.local
    named.conf.options
    named.conf.log
2. zone files
    /etc/bind/db.domain.com
    zone file describes a zone completely. There must be precisely one SOA record and at least one NS record.
3. reverse name resolution files
    contain all PTR record
    /etc/bind/db.10.129.14


## Interesting parameters 

allow-query	    Defines which hosts are allowed to send requests to the DNS server.
allow-recursion	Defines which hosts are allowed to send recursive requests to the DNS server.
allow-transfer	Defines which hosts are allowed to receive zone transfers from the DNS server.
zone-statistics	Collects statistical data of zones.

# Zone transfer

The Primary DNS server (Master) holds the master copy of DNS records in its datbase.

The secondary DNS server (Slave) holds the redundant copy of DNS records.

Slave requests the Master for DNS update. 2 types of updtaes :
- AXFR : Full zone transfer
- IXFR : Incremental zone transfer

Zone transfer should be enable on the Master DNS server.

A user can send the same request that the secondary DNS server to get a copy of all the records (sundomains available).

Generally happens over TCP port 53.
Using a secret key rndc-key.

# Footprinting the service

dig ns inlanefreight.htb @$TARGET
dig CH TXT version.bind $TARGET
dig any inlanefreight.htb @$TARGET
dig axfr www.inlanefreight.htb @$TARGET

## Enumeration

wget -c https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt -O subdomains-top1million-110000.txt

wget -c https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt -O subdomains-top1million-110000.txt

git clone --depth 1 \
  https://github.com/danielmiessler/SecLists.git

export WORDLIST="SecLists/Discovery/DNS/subdomains-top1million-20000.txt"


dnsenum --dnsserver $TARGET --enum -p 0 -s 0 -o subdomains.txt -f subdomains-top1million-110000.txt inlanefreight.htb

dnsenum --dnsserver $TARGET --enum -p 0 -s 0 -o subdomains.txt -f deepmagic.com-prefixes-top500.txt inlanefreight.htb

dnsenum --dnsserver $TARGET --enum -p 0 -s 0 -o subdomains.txt -f $WORDLIST --threads 90 root.inlanefreight.htb


## Interaction

## Subdomain Brute Forcing

wget -c https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt -O subdomains-top1million-5000.txt 

for sub in $(cat subdomains-top1million-5000.txt);do dig $sub.inlanefreight.htb @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

for sub in $(cat SecLists/Discovery/DNS/subdomains-top1million-20000.txt);do dig $sub.inlanefreight.htb @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

for sub in $(cat SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

export WORDLIST="SecLists/Discovery/DNS/subdomains-top1million-110000.txt"

for sub in $(cat $WORDLIST);do dig $sub.inlanefreight.htb @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

