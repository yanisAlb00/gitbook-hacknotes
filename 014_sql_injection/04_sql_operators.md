# SQL Operators

condition1 AND condition2
SELECT 1 = 1 AND 'test' = 'test';

SELECT 1 = 1 OR 'test' = 'abc';
SELECT NOT 1 = 1;

SELECT 1 = 1 && 'test' = 'abc';

SELECT * FROM logins WHERE username != 'john';

SELECT * FROM logins WHERE username != 'john' AND id > 1;

SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;

SELECT * FROM logins WHERE username != 'tom' AND id > 1;

select * from logins where username != 'tom' AND id > 3 - 2;
