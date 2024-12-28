# INTRO

| Category                                         | Applications                                                           |
|--------------------------------------------------|------------------------------------------------------------------------|
| Web Content Management                           | Joomla, Drupal, WordPress, DotNetNuke, etc.                            |
| Application Servers                              | Apache Tomcat, Phusion Passenger, Oracle WebLogic, IBM WebSphere, etc. |
| Security Information and Event Management (SIEM) | Splunk, Trustwave, LogRhythm, etc.                                     |
| Network Management                               | PRTG Network Monitor, ManageEngine Opmanger, etc.                      |
| IT Management                                    | Nagios, Puppet, Zabbix, ManageEngine ServiceDesk Plus, etc.            |
| Software Frameworks                              | JBoss, Axis2, etc.                                                     |
| Customer Service Management                      | osTicket, Zendesk, etc.                                                |
| Search Engines                                   | Elasticsearch, Apache Solr, etc.                                       |
| Software Configuration Management                | Atlassian JIRA, GitHub, GitLab, Bugzilla, Bugsnag, Bitbucket, etc.     |
| Software Development Tools                       | Jenkins, Atlassian Confluence, phpMyAdmin, etc.                        |
| Enterprise Application Integration               | Oracle Fusion Middleware, BizTalk Server, Apache ActiveMQ, etc.        |

## Module

On this module web applications are hosted behind vhosts on one unique server :

IP=10.129.42.195
printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts

