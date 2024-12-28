# 04 ftp

## FTP

The FTP runs within the application layer of the TCP/IP protocol stack. Thus, it is on the same layer as HTTP or POP.

Contol chanel between client and server : TCP/21 Data chanel between client and server : TCP/20

Active/passive mode :&#x20;

In passive mode, the server announces a port and the client initiates the connection.&#x20;

In active mode, the client informs the server via which client-side port the server can transmit its responses. ( can be blocked by firewall).

https://en.wikipedia.org/wiki/List\_of\_FTP\_server\_return\_codes

Usually, we need credentials to use FTP on a server. FTP is a clear-text protocol that can sometimes be sniffed if conditions on the network are right. However, there is also the possibility that a server offers anonymous FTP.

## TFTP

TFTP is simpler than FTP and use UDP.

It is impossible to implement authentication in TFTP.

## Configuration

```
sudo apt install vsftpd
```

```
/etc/vsftpd.conf
```

### Interesting parameters

anonymous\_enable=YES|NO

/etc/ftpusers : use to deny certain users access to FTP service even if they are present on the Linux system

hide\_ids=YES -> all user and group information are displayed as "ftp" (making it more diffcult to identify from which user the files are written and downloaded)

ls\_recurse\_enable=YES -> allow us to have an overview of the FTP directory structure and see all the visible content at once

## Commands

```
ftp -p $TARGET
```

```
ftp> status 
ftp> debug 
ftp> trace 
ftp> ls
```

Download all the files :&#x20;

```
wget -m --no-passive ftp://anonymous:anonymous@$TARGET
tree .
```

```
ftp -p 10.129.23.177 2121
```

```
wget -m --no-passive ftp://ceil:qwer1234@10.129.68.3:2121
wget -r --ftp-user=ceil --ftp-password=qwer1234 ftp://10.129.162.125:2121/
```

## Footprinting the service

### Enumeration

```
sudo nmap --script-updatedb find / -type f -name ftp* 2>/dev/null | grep scripts
```

NSE scripts located in /usr/share/nmap/scripts/

```
sudo nmap -sV -p21 -sC -A $TARGET sudo nmap -sV -p21 -sC -A $TARGET --script-trace
```



### Interaction

nc -nv $TARGET 21 telnet $TARGET 21

If FTP uses TLS/SSL Encryption : openssl s\_client -connect $TARGET:21 -starttls ftp
