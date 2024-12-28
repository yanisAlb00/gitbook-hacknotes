# Drupal - Discovery & Enumeration

Drupal is written in PHP and supports using MySQL or PostgreSQL for the backend. 

Additionally, SQLite can be used if there's no DBMS installed.

## Discovery/Footprinting

curl -s http://drupal.inlanefreight.local | grep Drupal

<meta name="Generator" content="Drupal 8 (https://www.drupal.org)" />
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>

Another way to identify :
http://drupal.inlanefreight.local/node/1

Drupal supports three types of users by default:
- Administrator: This user has complete control over the Drupal website.
- Authenticated User: These users can log in to the website and perform operations such as adding and editing articles based on their permissions.
- Anonymous: All website visitors are designated as anonymous. By default, these users are only allowed to read posts.

## Enumeration

Newer installs of Drupal by default block access to the CHANGELOG.txt and README.txt

curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""
Drupal 7.57, 2018-02-21

CHANGELOG.txt not blocked --> older version of drupal

https://github.com/droope/droopescan
sudo pip3 install droopescan
droopescan scan drupal -u http://drupal.inlanefreight.local

## LAB

IP=10.129.75.135
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local drupal.inlanefreight.local drupal-qa.inlanefreight.local" | sudo tee -a /etc/hosts

sudo pip3 install droopescan
droopescan scan drupal -u drupal-qa.inlanefreight.local

[+] Possible version(s):
    7.30
