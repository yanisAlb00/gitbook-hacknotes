## Using Comments

Normal comment :
SELECT username FROM logins; -- Selects usernames from the logins table 
SELECT * FROM logins WHERE username = 'admin'; # You can place anything here AND password = 'something'

SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
--> same that SELECT * FROM logins where (username='admin')

