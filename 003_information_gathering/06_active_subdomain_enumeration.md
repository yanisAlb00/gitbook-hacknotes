# Active Subdomain Enumeration

## ZoneTransfers

The zone transfer is how a secondary DNS server receives information from the primary DNS server and updates it.

Slaves receive updated DNS information from the master DNS.

https://hackertarget.com/zone-transfer/

1. Identifying Nameservers

nslookup -type=NS inlanefreight.htb

2. Testing for ANY and AXFR Zone Transfer

nslookup -type=any -query=AXFR zonetransfer.me nsztm1.digi.ninja

dig ns @10.129.72.185 inlanefreight.htb
dig axfr @$TARGET inlanefreight.htb
dig inlanefreight.htb
    ;; AUTHORITY SECTION:
.			86400	IN	SOA	a.root-servers.net. nstld.verisign-grs.com. 2023092100 1800 900 604800 86400

dig any a.root-servers.net @$TARGET

dig CH TXT a.root-servers.net $TARGET
dig CH TXT nstld.verisign-grs.com $TARGET


## Gobuster

export TARGET="facebook.com"
export NS="d.ns.facebook.com"
export WORDLIST="numbers.txt"

git clone --depth 1 \
  https://github.com/danielmiessler/SecLists.git

export WORDLIST="SecLists/Discovery/DNS/subdomains-top1million-20000.txt"

gobuster dns -q -r "${NS}" -d "${TARGET_DOMAIN}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"

vi patterns.txt

lert-api-shv-{GOBUSTER}-sin6
atlas-pp-shv-{GOBUSTER}-sin6

dnsenum --dnsserver $TARGET --enum -p 0 -s 0 -o subdomains.txt -f $WORDLIST --threads 90 a.root-servers.net

ns.inlanefreight.htb.                    604800   IN    A         127.0.0.1
admin.inlanefreight.htb.                 604800   IN    A        10.10.34.2
www1.inlanefreight.htb.                  604800   IN    A        10.10.34.111
test1.inlanefreight.htb.                 604800   IN    A        10.10.34.101
us.inlanefreight.htb.                    604800   IN    A        10.10.200.5
careers.inlanefreight.htb.               604800   IN    A        10.10.34.50
resources.inlanefreight.htb.             604800   IN    A        10.10.34.100
ir.inlanefreight.htb.                    604800   IN    A        10.10.45.5
dc1.inlanefreight.htb.                   604800   IN    A        10.10.34.16
dc2.inlanefreight.htb.                   604800   IN    A        10.10.34.11

dig axfr inlanefreight.htb @$TARGET >dns.txt
cat dns.txt | while read i; do dig axfr $i @$TARGET; echo $i; done > enumerated.txt

nslookup -query=axfr inlanefreight.htb $TARGET | grep “Name:” | cut -d “:” -f2 | while read ZONE; do nslookup -query=axfr $ZONE $TARGET; done > zones.txt


## LAB

nslookup -type=any -query=AXFR inlanefreight.htb $TARGET
nslookup -type=any -query=AXFR inlanefreight.htb $TARGET | grep "Name" > rawsubdomains.txt 
sed 's/Name://g' rawsubdomains.txt > subdomains.txt

Remove spaces at the beginning :

cat subdomains.txt | sed -e 's/^[ \t]*//' > subdomains.txt

while read -r line; do
  nslookup -type=any -query=AXFR $line $TARGET >> results.txt
done < subdomains.txt

dig axfr inlanefreight.htb @$TARGET
dig axfr internal.inlanefreight.htb @$TARGET