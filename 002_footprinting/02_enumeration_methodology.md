# Methodology

3 different levels :

1. Infrastructure-based enumeration
2. Host-based enumeration	
3. OS-based enumeration

This methodology is nested in 6 layers :

-- Infrastructure-based enumeration
## 1. Internet presence

Description : Identification of internet presence and externally accessible infrastructure.	

Information categories : Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures

## 2. Gateaway

Description : Identify the possible security measures to protect the company's external and internal infrastructure.

Information categories : Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare

-- Host-based enumeration
## 3. Accessible services

Description : Identify accessible interfaces and services that are hosted externally or internally.	

Information categories : Service Type, Functionality, Configuration, Port, Version, Interface

## 4. Processes

Description : Identify the internal processes, sources, and destinations associated with the services.

Information categories : PID, Processed Data, Tasks, Source, Destination

-- OS-based enumeration
## 5. Privileges

Description : Identification of the internal permissions and privileges to the accessible services.	

Information categories : Groups, Users, Permissions, Restrictions, Environment

## 6. OS Setup

Description : Identification of the internal components and systems setup.	

Information categories : OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files

