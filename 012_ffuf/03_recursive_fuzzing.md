# Recursive Fuzzing

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v

## LAB

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.52.253:55616/FUZZ -recursion -recursion-depth 1 -e .php -v > fuzz.txt

94.237.52.253:55616/forum/flag.php
HTB{fuzz1n6_7h3_w3b!}