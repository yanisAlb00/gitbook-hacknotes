# Sshuttle

Sshuttle is another tool written in Python which removes the need to configure proxychains. 

sudo apt-get install sshuttle
sudo sshuttle -r ubuntu@$TARGET 172.16.5.0/23 -v 
nmap -v -sV -p3389 172.16.5.19 -A -Pn