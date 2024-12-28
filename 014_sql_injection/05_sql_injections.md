# Intro to SQL Injections

## Use of SQL in Web Applications

$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);

$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);

## Injections

'%1'; DROP TABLE users;'

3 categories :

1. In-band
Union Based : the query will direct the output to be printed there
Error Based : we can get the PHP or SQL errors in the front-end

2. Blind
Boolean Based : use SQL conditional statements to control whether the page returns any output at all
Time Based : use SQL conditional statements that delay the page response if the conditional statement returns true using the Sleep() function

3. Out-of-band : No access to output
--> need to redirect the output (using DNS ?)