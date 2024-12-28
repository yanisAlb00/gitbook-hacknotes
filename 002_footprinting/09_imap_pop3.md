# IMAP and POP3

IMAP : client-server-based network protocol wich allows synchronization of a local email client with the mailbox on the server.

-> IMAP must be used for additional functionalities such as hierarchical mailboxes directly at the mail server, access to multiple mailboxes during a session, and preselection of emails.

POP3 : only provides listing, retrieving, and deleting emails as functions at the email server. 

-> POP3 offers less possibilities than IMAP


## Connection to the server from the client

Client -> Server : port TCP/143

Text-based Format : ASCII

Mails are stored on the server in a uniform database.

Clients access these structures online and can create local copies. 

IMAP is text-based and has extended functions, such as browsing emails directly on the server.

Without an active connection to the server, managing emails is impossible.

Some clients offer an offline mode with a local copy of the mailbox and synchronizes all offline local changes when a connection is reestablished.

Authentication with username and password to access mailboxes.

SSL/TLS possible on port 143 or 993 for example.

# Configuration


## Interesting parameters 
auth_anonymous_username	
auth_verbose_passwords	
auth_debug_passwords	


# Commands

IMAP
1 LOGIN username password	
1 CREATE "INBOX"	
1 DELETE "INBOX"	
1 LOGOUT


POP3
USER username	
PASS password
STAT
DELE id	

# Footprinting the service

By default, ports 110, 143, 993, and 995 are used for IMAP and POP3. (993 & 995 for SSL)

## Enumeration

sudo nmap $TARGET -sV -p110,143,993,995 -sC
curl -k 'imaps://$TARGET' --user robin:robin
curl -k 'imaps://$TARGET' --user robin:robin -v

openssl s_client -connect $TARGET:pop3s
openssl s_client -connect $TARGET:imaps

## Interaction

telnet $TARGET 143

1 LOGIN robin robin
1 LIST "" *
1 SELECT DEV.DEPARTMENT.INT
1 STATUS DEV.DEPARTMENT.INT (MESSAGES)
1 FETCH 1 all
1 FETCH 1 (BODY[HEADER])
1 FETCH 1 (BODY[HEADER.FIELDS (Subject)])
1 FETCH 1 (BODY[TEXT])
HTB{983uzn8jmfgpd8jmof8c34n7zio}

## Subdomain Brute Forcing
