# Query Results

## Sorting Results

SELECT * FROM logins ORDER BY password;
SELECT * FROM logins ORDER BY password DESC;
SELECT * FROM logins ORDER BY password DESC, id ASC;

## LIMIT results

SELECT * FROM logins LIMIT 2;
SELECT * FROM logins LIMIT 1, 2;

## WHERE

SELECT * FROM logins WHERE id > 1;
SELECT * FROM logins where username = 'admin';

## LIKE

SELECT * FROM logins WHERE username LIKE 'admin%';
SELECT * FROM logins WHERE username like '___';

the _ symbol is used to match exactly one character

## LAB

tom' or '1'='1'
admin'-- '
toto' OR id=5) --