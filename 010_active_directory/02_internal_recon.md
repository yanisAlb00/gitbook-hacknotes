# Internal Recon

Key Data Points :
- AD Users : We are trying to enumerate valid user accounts we can target for password spraying.
- AD Joined Computers : Key Computers include Domain Controllers, file servers, SQL servers, web servers, Exchange mail servers, database servers, etc.
- Key Services : Kerberos, NetBIOS, LDAP, DNS
- Vulnerable Hosts and Services : Anything that can be a quick win. ( a.k.a an easy host to exploit and gain a foothold)

sudo -E wireshark
sudo tcpdump -i ens224 
sudo responder -I ens224 -A
fping -asgq 172.16.5.0/23
sudo nmap -v -A -iL hosts.txt -oN /home/htb-student/Documents/host-enum
nmap -A 172.16.5.100

## Kerbrute

sudo git clone https://github.com/ropnop/kerbrute.git
ls dist/
./kerbrute_linux_amd64 

-> Adding the Tool to our Path to make it easily accessible from anywhere on the host.

sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users

