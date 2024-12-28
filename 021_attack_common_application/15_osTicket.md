# osTicket

osTicket is an open-source support ticketing system. It can be compared to systems such as Jira, OTRS, Request Tracker, and Spiceworks.

osTicket is written in PHP and uses a MySQL backend. It can be installed on Windows or Linux.

## Footprinting/Discovery/Enumeration

### Eyewitness

sudo apt install eyewitness
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness

--> Cookie named OSTSESSID was set when visiting the page

The footer may also contain the words Support Ticket System

An Nmap scan will just show information about the webserver, such as Apache or IIS, and will not help us footprint the application.

### User Input

For instance, from the osTicket documentation, we can see that only staff and users with administrator privileges can access the admin panel :
https://docs.osticket.com/en/latest/Getting%20Started/Post-Installation.html

--> we can cause a problem and "play dumb" and contact the company's staff

### Processing

Suppose staff and administrators suspect that there is an internal bug that may be affecting the business. In that case, they will go into more detail to uncover possible code errors and address more significant issues.

### Solution

Very likely, other staff members from the technical departments will be involved in the email correspondence. This will give us new email addresses to use against the osTicket admin panel

## Attacking osTicket

osTicket version 1.14.1 suffers from https://nvd.nist.gov/vuln/detail/CVE-2020-24881
which was an SSRF vulnerability

https://medium.com/intigriti/how-i-hacked-hundreds-of-companies-through-their-helpdesk-b7680ddc2d4c

## osTicket - Sensitive Data Exposure

Starting points :

1) Several user credentials using Dehashed

sudo python3 dehashed.py -q inlanefreight.local -p

2) Subdomain enumeration and come across several interesting ones

cat ilfreight_subdomains

vpn.inlanefreight.local
support.inlanefreight.local
ns1.inlanefreight.local
mail.inlanefreight.local
apps.inlanefreight.local
ftp.inlanefreight.local
dev.inlanefreight.local
ir.inlanefreight.local
auth.inlanefreight.local
careers.inlanefreight.local
portal-stage.inlanefreight.local
dns1.inlanefreight.local
dns2.inlanefreight.local
meet.inlanefreight.local
portal-test.inlanefreight.local
home.inlanefreight.local
legacy.inlanefreight.local

3) Try credentials against URL

4) Inspect open tickets and if there are some information inside

## LAB

IP=10.129.201.88
printf "%s\t%s\n\n" "$IP" "support.inlanefreight.local" | sudo tee -a /etc/hosts

charles.smithson@inlanefreight.local

--> Create ticket
hacker@hacker.com

http://support.inlanefreight.local/account.php?do=create


support@inlanefreight.local

git clone https://github.com/grahamhelton/dehashQuery
sudo python3 dehashed.py -q support.inlanefreight.local -p

I'm an agent sign here

kevin@inlanefreight.local
Fish1ng_s3ason!

http://support.inlanefreight.local/scp/

Inlane_welcome!