# Page Fuzzing

## Extension Fuzzing

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ

.php                    [Status: 200, Size: 0, Words: 1, Lines: 1]
.phps                   [Status: 403, Size: 283, Words: 20, Lines: 10]

-> The Website runs in PHP

## Page Fuzzing

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php

index                   [Status: 200, Size: 0, Words: 1, Lines: 1]
REDACTED                [Status: 200, Size: 465, Words: 42, Lines: 15]

## LAB

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://94.237.52.253:55616/blog/indexFUZZ

.php                    [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 1ms]
:: Progress: [39/39] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors:.phps                   [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 3ms]


ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.52.253:55616/blog/FUZZ.php > fuzz.txt

cat fuzz.txt | grep "Status: 200"

http://94.237.52.253:55616/blog/home.php

home                    [Status: 200, Size: 1046, Words: 438, Lines: 58, Duration: 0ms]

HTB{bru73_f0r_c0mm0n_p455w0rd5}