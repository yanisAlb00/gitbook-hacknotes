# Identifying Filters

## Filter/WAF Detection

WAF is enabled if for operators like (;, &&, ||), we get the error message invalid input:

If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF.

## Blacklisted Characters

PHP code may look something like the following:

$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}


## Identifying Blacklisted Character

ip=127.0.0.1§;§+whoami

NOT URL Encoded

ip=127.0.0.1&+whoami