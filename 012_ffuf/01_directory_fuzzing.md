# Directory Fuzzing

If not installed :
apt install ffuf -y
https://github.com/ffuf/ffuf.git

ffuf -h

Directory Fuzzing :
ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://TARGET:PORT/FUZZ

Faster by adding number of threads (but risk of Denial of Service):
-t 200

## LAB

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://83.136.255.120:48171/FUZZ > fuzz.txt

cat fuzz.txt | grep "Status: 301"
blog                    [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 0ms]
forum                   [Status: 301, Size: 325, Words: 20, Lines: 10, Duration: 0ms]