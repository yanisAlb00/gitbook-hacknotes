# Application Discovery & Enumeration

Objective : provide a global overview of hosts and services and potential vulnerabilities to our customers

nmap -p 80,443,8000,8080,8180,8888,1000 --open -oA web_discovery -iL scope_list

If we got a lot of results from nmap, there are 2 Tools to ingest a large amount of data :
https://github.com/RedSiege/EyeWitness
https://github.com/michenriksen/aquatone

--> take screenshots of each

## Getting Organized

Build the skeleton of the report and fill certain sections of the report while waiting for a scan to finish :

Example structure :

External Penetration Test - <Client Name>

    Scope (including in-scope IP addresses/ranges, URLs, any fragile hosts, testing timeframes, and any limitations or other relative information we need handy)

    Client Points of Contact

    Credentials

    Discovery/Enumeration
        
        Scans
        Live hosts

    Application Discovery
        
        Scans
        Interesting/Notable Hosts
    
    Exploitation
        
        <Hostname or IP>
        <Hostname or IP>

    Post-Exploitation
        
        <Hostname or IP>
        <<Hostname or IP>

## Initial Enumeration

cat scope_list 

app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50

### Nmap

sudo  nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list 

Enumerating one of the hosts further using an Nmap service scan (-sV) :
sudo nmap --open -sV 10.129.201.50

### Using EyeWitness

sudo apt install eyewitness
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness

[*] Done! Report written in the /home/mrb3n/Projects/inlanfreight/inlanefreight_eyewitness folder!
Would you like to open the report now? [Y/n]

### Using Aquatone

wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
unzip aquatone_linux_amd64_1.7.0.zip 

Archive:  aquatone_linux_amd64_1.7.0.zip
  inflating: aquatone                
  inflating: README.md               
  inflating: LICENSE.txt 

sudo mv aquatone /usr/local/bin/aquatone

echo $PATH
/home/mrb3n/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games

cat web_discovery.xml | ./aquatone -nmap

Wrote HTML report to: aquatone_report.html

## LAB

### /etc/hosts 

IP=10.129.42.195
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local drupal-dev.inlanefreight.local drupal-qa.inlanefreight.local drupal-acc.inlanefreight.local drupal.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts

### NMAP

vi scope_list
app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog.inlanefreight.local


nmap -p 80,443,8000,8080,8180,8888,1000 --open -oA web_discovery -iL scope_list

### eyewitness

sudo apt install eyewitness
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness

ls -l /home/htb-ac-786011/inlanefreight_eyewitness
total 300
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011 167936 Jan  8 20:28 ew.db
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011  95957 Sep 15  2021 jquery-1.11.3.min.js
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011    289 Jan  8 20:28 open_ports.csv
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011  10144 Jan  8 20:28 report.html
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011   3169 Jan  8 20:28 report_page2.html
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011   1634 Jan  8 20:28 Requests.csv
drwxr-xr-x 2 htb-ac-786011 htb-ac-786011   4096 Jan  8 20:28 screens
drwxr-xr-x 2 htb-ac-786011 htb-ac-786011   4096 Jan  8 20:28 source
-rw-r--r-- 1 htb-ac-786011 htb-ac-786011    684 Jan  8 20:28 style.css

### aquatone

wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
unzip aquatone_linux_amd64_1.7.0.zip
sudo apt install chromium/parrot-security
cat web_discovery.xml | ./aquatone -nmap

From chromium browser :
file:///home/htb-ac-786011/aquatone_report.html#/
Pages by Similarity
