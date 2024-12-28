# Vhost Fuzzing

VHost is basically a 'sub-domain' served on the same server.

VHosts may or may not have public DNS records.

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'

--> fuzzing HTTP headers to not add the entire wordlist to /etc/hosts

# Filtering Results

ffuf -h

If 900 is the size of the incorrect results :

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900

## LAB

sudo sh -c 'echo "94.237.56.124 academy.htb" >> /etc/hosts'

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:52583/ -H 'Host: FUZZ.academy.htb' -fs 900 > ffuf-vhost.txt

ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:52583/ -H 'Host: FUZZ.academy.htb' -fs 986 > ffuf-vhost2.txt

test                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
admin                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1ms]

test.academy.htb