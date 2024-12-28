# SNMP

SNMP is a protocol for monitoring and managing network devices.

Control commands using agents over UDP port 161

SNMP also enables the use of so-called traps over UDP port 162

traps : data packets sent from the SNMP server to the client without being explicitly requested (specific event occurs on the server-side)

## Storing devices information using MIB

Management Information Base (MIB) : storing of devices information. It contains at least one OID and is written in ASN.1 based on ASCII format.

In MIB, All queryable SNMP objects of a device are listed in a standardized tree hierarchy.

OID  = node in a hierarchical namespace. 

**SNMPv1 :**

- No built-in authentication
- Does not support encryption

**SNMPv2 :**

- The community string that provides security is only transmitted in plain text
- No built-in encryption

Community Strings <-> passwords


**SNMPv3 :**

- authentication using username and password
- transmission encryption (via pre-shared key) of the data

# Configuration

cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'

## Interesting parameters 

rwuser noauth : Provides access to the full OID tree without authentication.
rwcommunity <community string> <IPv4 address> : Provides access to the full OID tree regardless of where the requests were sent from.
rwcommunity6 <community string> <IPv6 address> : Same access as with rwcommunity with the difference of using IPv6.

# Footprinting the service


snmpwalk -> query OIDs and their information

snmpwalk -v2c -c public $TARGET

onesixtyone -> brute-force the names of the community strings

sudo apt install onesixtyone
onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt $TARGET

braa -> brute-force individual OIDs once we know a community string

sudo apt install braa
braa <community string>@<IP>:.1.3.6.* 

