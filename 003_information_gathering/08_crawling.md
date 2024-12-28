# Crawling

## ZAP

Belongs to the OWASP.

Contains built-in Fuzzer and Manual Request Editor

## FFUF

ffuf -recursion -recursion-depth 1 -u http://$TARGET/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/raft-small-directories-lowercase.txt

-> ffuf creates new jobs for every detected folder.

## Sensitive information

Extract key words from the website :

cewl -m5 --lowercase -w wordlist.txt http://$TARGET


ffuf -w ./folders.txt:FOLDERS,./wordlist.txt:WORDLIST,./extensions.txt:EXTENSIONS -u http://$TARGET/FOLDERS/WORDLISTEXTENSIONS

curl http://$TARGET/wp-content/secret~
