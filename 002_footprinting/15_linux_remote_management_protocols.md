# Linux Remote Management Protocols

## SSH

TCP/22
OpenSSH

SSH1 vulnerable to MITM attacks and not SSH2

OpenSSH has six different authentication methods:

1. Password authentication
2. Public-key authentication
passphrase : local encryption of the private-key
3. Host-based authentication
4. Keyboard authentication
5. Challenge-response authentication
6. GSSAPI authentication

### Default configuration

cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'

#### Interesting parameters

PasswordAuthentication yes
PermitEmptyPasswords yes
PermitRootLogin yes
Protocol 1
X11Forwarding yes
AllowTcpForwarding yes
PermitTunnel
DebianBanner yes

### Footprinting the Service

#### Enumeration

git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
./ssh-audit.py $TARGET

ssh -o PubkeyAuthentication=no -o PreferredAuthentications=password  example.com


#### Interaction

ssh -v cry0l1t3@$TARGET
ssh -v cry0l1t3@$TARGET -o PreferredAuthentications=password

## Rsync

Rsync is a fast and efficient tool for locally and remotely copying files.

TCP/873

### Footprinting the Service

#### Enumeration

sudo nmap -sV -p 873 $TARGET

#### Interaction

nc -nv $TARGET 873
list
-> find a share named "dev"
rsync -av --list-only rsync://$TARGET/dev
-> enumerate "dev" share

## R-Services

R-Services are a suite of services over TCP/IP :

rcp (remote copy)
rexec (remote execution)
rlogin (remote login)
rsh (remote shell)
rstat
ruptime
rwho (remote who)

Much like telnet, r-services transmit information over the network in an unencrypted format.

Default ports : 512,513,514

cat /etc/hosts.equiv : list of trusted hosts and is used to grant access to other systems on the network.

### Footprinting the Service

#### Enumeration

sudo nmap -sV -p 512,513,514 $TARGET

#### Interaction

cat .rhosts
-> .rhosts provides a per-user configuration

rlogin $TARGET -l htb-student
-> Logging in Using Rlogin
rwho
-> Listing Authenticated Users Using Rwho
rusers -al $TARGET
-> Listing Authenticated Users Using Rusers
