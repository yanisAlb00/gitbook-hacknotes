# Web Access Control

## DAC : Discretionary Access Control

Resource owner or administrator determines who is allowed to access a resource and what actions they are allowed to perform.

\-> Only the data identified by the owner or administrator are concerned by access control policy and not all

## MAC : Mandatory Access Control

Administrator determines :&#x20;

* Users clearance level
* Data confidentiality level
* Global rules

And the access is granted or denied regarding users and data level regarding the global rules which are implemented.

\-> All data and all users are supposed to be concerned by access control policy

## RBAC : Role-Based Access Control

Each user is associated to a role (admin, IT, HR, Financial)

Each role is associated to a list of permisssions.

A permission is the combinaison of an operation and privileges.

Example : Toto has been assigned the IT Role which contains a permission to read-only CMDB

\-> All Users and Data are concerned by access control policy

## ABAC : Attribute-Based Access Control

Access is controled by roles but also other attributes such as timestamp, device, location. These information are associated to subjects such as subject attributes.

\-> All Users and Data are concerned by access control policy

## Broken Access Control

* **Horizontal privilege escalation** : Attacker can access data belonging to other users with the same level of access
* **Vertical privilege escalation** : Attacker can access data belonging to other users with higher privileges
* **Insufficient access control checks** : Access Control checks are not properly implemented
* **IDOR** : Use of predictable or easily guessable identifier associated to a user
