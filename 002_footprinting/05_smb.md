# SMB

Server Message Block (SMB) is a client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers, or interfaces released for the network.

Information exchange between different processes can ba handled by SMB Protocol.

free software project Samba : solution that enables the use of SMB in Linux and Unix distributions
Samba implements the Common Internet File System (CIFS) network protocol

Using of TCP : For the connections and the subsequent transport of data

An SMB Server can provide its local file system as shares.

Access rights are defined by ACLs and can be controlled in a fine-grained manner such as execute, read, full access attributes for users or groups.


CIFS : use TCP/445 Only

NBNS : NetBios Name Server - In a NetBIOS environment, when a machine goes online, it needs a name, which is done through the so-called name registration procedure.

It also has been enhanced to Windows Internet Name Service (WINS).

# Configuration

cat /etc/samba/smb.conf | grep -v "#\|\;"
sudo systemctl restart smbd

## Interesting parameters 

browseable = yes
unix password sync = yes	
usershare allow guests = yes
map to guest = bad user	
create mask = 0700	What permissions need to be set for newly created files?

# Commands

using an exclamation mark at the beginning (!<cmd>) to avoid interrupting the connection

smbclient -N -L //$TARGET
smbclient //$TARGET/notes
smbclient //$TARGET/sambashare -U ziwicdhj
!ls
!cat prep-prod.txt
get prep-prod.txt
smbstatus

# Footprinting the service

## Enumeration

### Nmap

sudo nmap $TARGET -sV -sC -p139,445

### rpcclient

rpcclient -U "" $TARGET
--> Shares enumeration
rpcclient $> srvinfo
rpcclient $> enumdomains
rpcclient $> querydominfo
rpcclient $> netshareenumall
rpcclient $> netsharegetinfo notes

--> Users enumeration
rpcclient $> enumdomusers
rpcclient $> queryuser <RID>
rpcclient $> querygroup <group_rid>

### smbmap and crackmapexec

smbmap -H $TARGET

crackmapexec smb $TARGET --shares -u '' -p ''

### enum4linux-ng

git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
pip3 install -r requirements.txt

./enum4linux-ng.py 10.129.214.238 -A


## Interaction

### Brute Forcing User RIDs

for i in $(seq 500 1100);do rpcclient -N -U "" $TARGET -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

samrdump.py $TARGET

