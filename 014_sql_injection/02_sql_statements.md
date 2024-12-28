# SQL Statements

INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');
INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');
SELECT * FROM logins;
DROP TABLE logins;
ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn;
UPDATE logins SET password = 'change_password' WHERE id > 1;
SELECT * FROM logins ORDER BY password DESC, id ASC;
SELECT * FROM logins LIMIT 2;
SELECT * FROM logins WHERE id > 1;
SELECT * FROM logins WHERE username LIKE 'admin%';