# Tomcat - Discovery & Enumeration

Apache Tomcat is an open-source web server that hosts applications written in Java. 

Tomcat is often less apt to be exposed to the internet (though).

## Discovery/Footprinting

Tomcat servers can be identified by the Server header in the HTTP response :
http://app-dev.inlanefreight.local:8080/invalid

curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat 
<html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 9 (9.0.30) - Documentation Index</title><meta name="author" 

General folder structure of a Tomcat installation :

├── bin
├── conf
│   ├── catalina.policy
│   ├── catalina.properties
│   ├── context.xml
│   ├── tomcat-users.xml
│   ├── tomcat-users.xsd
│   └── web.xml
├── lib
├── logs
├── temp
├── webapps
│   ├── manager
│   │   ├── images
│   │   ├── META-INF
│   │   └── WEB-INF
|   |       └── web.xml
│   └── ROOT
│       └── WEB-INF
└── work
    └── Catalina
        └── localhost

Each folder inside webapps is expected to have the following structure.

  
webapps/customapp
├── images
├── index.jsp
├── META-INF
│   └── context.xml
├── status.xsd
└── WEB-INF
    ├── jsp
    |   └── admin.jsp
    └── web.xml
    └── lib
    |    └── jdbc_drivers.jar
    └── classes
        └── AdminServlet.class   


The most important file among these is WEB-INF/web.xml : deployment descriptor

Here’s an example web.xml file :

<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app>
  <servlet>
    <servlet-name>AdminServlet</servlet-name>
    <servlet-class>com.inlanefreight.api.AdminServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>AdminServlet</servlet-name>
    <url-pattern>/admin</url-pattern>
  </servlet-mapping>
</web-app>   

tomcat-users.xml file is used to allow or disallow access to the /manager and host-manager admin pages :

<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />

<!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui" />


</tomcat-users>

## Enumeration

gobuster dir -u http://web01.inlanefreight.local:8180/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt 

Using weak credentials such as tomcat:tomcat, admin:admin

## LAB

IP=10.129.201.58
printf "%s\t%s\n\n" "$IP" "app-dev.inlanefreight.local web01.inlanefreight.local" | sudo tee -a /etc/hosts

curl -s http://web01.inlanefreight.local:8180/docs/ | grep Tomcat 

<html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 10 (10.0.10)