# Jenkins - Discovery & Enumeration

Jenkins is an open-source automation server written in Java that helps developers build and test their software projects continuously.

It is a server-based system that runs in servlet containers such as Tomcat. 

## Discovery/Footprinting

Jenkins runs on Tomcat port 8080 by default. It also utilizes port 5000 to attach slave servers. 

## Enumeration

http://jenkins.inlanefreight.local:8000/configureSecurity/

The default installation typically uses Jenkins’ database to store credentials and does not allow users to register an account. 

http://jenkins.inlanefreight.local:8000/login?from=%2F

Test weak or default credentials such as admin:admin 

## LAB

IP=10.129.201.58
printf "%s\t%s\n\n" "$IP" "jenkins.inlanefreight.local" | sudo tee -a /etc/hosts

http://jenkins.inlanefreight.local:8000/login?from=%2F

admin
admin

Jenkins 2.303.1