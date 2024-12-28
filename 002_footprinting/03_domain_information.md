# SSL Certificate

Check all the subdomains for which the SSL Certificate is used :
https://crt.sh/

Output result in json :
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq -> filtered by the unique subdomain

for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

# Shodan

Shodan can be used to find devices and systems permanently connected to the internet

for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
for i in $(cat ip-addresses.txt);do shodan host $i;done

# DNS Records

dig any inlanefreight.com

- A Record = IP addresses that points to a specific subdomain
- MX Records = Which mail server is responsible for managing email to the company
- NS Records = Which named servers are used to resolve FQDN to IP addresses
- TXT Records = Verification keys for different third-party providers and other security aspects of DNS, such as SPF, DMARC, and DKIM
-> TXT Records could lead to interesting information such as which Software are used by the company (Atlassian, Logmein, Google Gmail, Google Drive, Outlook)

inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"

 The TXT record with the "MS" value is often used to confirm the domain. In most cases, it is similar to the username or ID used to log in to the management platform.

# Cloud resources

 yanisAlb@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

Results :

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250

s3-website-us-west-2.amazonaws.com -> S3 Bucket

Google Dorks :
inurl:
intext:

domain.glass
https://buckets.grayhatwarfare.com/