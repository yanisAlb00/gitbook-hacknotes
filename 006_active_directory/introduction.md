# Introduction

## Definitions

A **forest** is a gathering of **domains**&#x20;

Into each domain there are **OU = Organizationnal Unit**&#x20;

Into each OU there are **objects** = users, workstations, printers ...&#x20;

Each object contain a list of **attributes** Objects can be gather in **groups**

## Different kind of groups

* Local groups = can be used only inside a domain
* Global groups = can be used in different domains which are relied by trust relation
* Universal groups = can be used in the whole forest
* Security groups = manage access rights
* Distribution groups = manage distribution list in Exchange service

\-> Good practice : establish a clear nomenclature for different kind of groups

**GPO (Group Policy Object)** : Apply security settings on domain objects. GPO can be set differently between objects or between OU.

## Domain Controller

Domain Controller = Windows Server with AD-DS Role

DNS Role has to be configured into each Domain Controller

5 FSMO roles :

* Schema master In charge of unicity of the schema (hierarchy)
* Domain naming master In charge of adding and deleting domain names
* PDC master NTP : One PDC per domain
* RID master Registered ID Master : Provide unique ID into a domain
* Infrastructure master in charge of the synchronization of the changes inside a domain

