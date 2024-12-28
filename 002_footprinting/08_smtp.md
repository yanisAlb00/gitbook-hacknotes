# SMTP

The Simple Mail Transfer Protocol (SMTP) is a protocol for sending emails in an IP network.

By default, SMTP servers accept connection requests on port 25. However, newer SMTP servers also use other ports such as TCP port 587. 

-> STARTTLS

If SMTP is used without encryption, it is possible to use it in conjonction with SSL/TLS : Often a different port than 25, 465 for example.

Client (MUA)	➞	Submission Agent (MSA)	➞	Open Relay (MTA)	➞	Mail Delivery Agent (MDA)	➞	Mailbox (POP3/IMAP)

Weaknesses of SMTP :
1. Sending an email using SMTP does not return a usable delivery confirmation.
2. Users are not authenticated when a connection is established, and the sender of an email is therefore unreliable. As a result, open SMTP relays are often misused to send spam en masse. The originators use arbitrary fake sender addresses for this purpose to not be traced (mail spoofing).

ESMTP : An extension for SMTP has been developed called Extended SMTP 
-> Initializes the SSL-protected SMTP connection, and from this moment on, the entire connection is encrypted, and therefore more or less secure. 

# Configuration

cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

## Interesting parameters 

mynetworks = 0.0.0.0/0


# Footprinting the service


## Enumeration

telnet $TARGET 25
CONNECT $TARGET:25 HTTP/1.0
sudo nmap $TARGET -sC -sV -p25
sudo nmap $TARGET -p25 --script smtp-open-relay -v
sudo nmap $TARGET -p25 --script smtp-commands -v
-> Include EHLO commands


HELO mail1.inlanefreight.htb
-> log in and start a session

EHLO mail1
-> list all the commands that can be executed

VRFY root
-> check if a mailbox is available for message transfer

SMTP server may issue code 252 and confirm the existence of a user that does not exist on the system. 

https://serversmtp.com/smtp-error/?doing_wp_cron=1694777189.1626520156860351562500



## Interaction

MAIL FROM: <cry0l1t3@inlanefreight.htb>
RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure
DATA
QUIT

## Usernames

pip install smtp-user-enum
vi footprinting-wordlist.txts
smtp-user-enum -U footprinting-wordlist.txt mail1.inlanefreight.htb 25
smtp-user-enum -U footprinting-wordlist.txt $TARGET 25
