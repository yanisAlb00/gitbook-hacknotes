# CoreFTP before build 727 

## The Concept of the Attack

Service allows HTTP POST requests and HTTP PUT requests

CoreFTP Exploitation
curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops

Directory Traversal

- Source

The user specifies the type of HTTP request with the file's content, including escaping characters to break out of the restricted area.

- Process

HTTP request, file contents, and path entered are taken over and processed by the process.

- Privileges

Since the restrictions only apply to a specific folder, all permissions granted to it are bypassed as it breaks out of that folder using the directory traversal.

- Destination

The destination is another process that has the task of writing the specified contents of the user on the local system.	