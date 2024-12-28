# Intro to MySQL

SQL can be used to perform the following actions:

- Retrieve data
- Update data
- Delete data
- Create new tables and databases
- Add / remove users
- Assign permissions to these users

mysql -u root -h 94.237.62.6 -P 58375 -p
SHOW DATABASES;
CREATE DATABASE users;
USE users;
CREATE TABLE logins (     id INT,     username VARCHAR(100),
  password VARCHAR(100),     date_of_joining DATETIME     );
SHOW TABLES;
DESCRIBE logins;
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
