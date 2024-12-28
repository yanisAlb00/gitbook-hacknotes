# Sub-domain Fuzzing

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/

Be careful to add domain in /etc/hosts :
sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'

## LAB
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/ > fuzz-subdomain.txt

customer                [Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 39ms]

customer.inlanefreight.com