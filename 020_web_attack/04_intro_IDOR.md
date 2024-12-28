# Intro to IDOR

IDOR vulnerabilities occur when a web application exposes a direct reference to an object, like a file or a database resource, which the end-user can directly control to obtain access to other similar objects.

If a user request is : download.php?file_id=123
What happened if we try : download.php?file_id=124

What Makes an IDOR Vulnerability ?
Weak access control system.

## Prevention
--> Restricted acces to pages, functions, APIs and apply Role-Based Access Control (RBAC) system
 
Object-Level Access Control :

match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}

Object Referencing :
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result));
echo "<a href='" . $row['url'] . "' target='_blank'></a>";

--> Never calculate hashes on the front-end


