# Intro to Command Injections

If a web application uses user-controlled input to execute a system command on the back-end server to retrieve and return specific output, we may be able to inject a malicious payload to subvert the intended command and execute our commands.

Injection	Description
OS Command Injection	Occurs when user input is directly used as part of an OS command.
Code Injection	Occurs when user input is directly within a function that evaluates code.
SQL Injections	Occurs when user input is directly used as part of an SQL query.
Cross-Site Scripting/HTML Injection	Occurs when exact user input is displayed on a web page.

Others like LDAP injection, NoSQL Injection, HTTP Header Injection, XPath Injection, IMAP Injection, ORM Injection, ...

OS Command Injections

PHP may use the exec, system, shell_exec, passthru, or popen functions to execute commands directly on the back-end server

<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>

NodeJS Example

app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})

