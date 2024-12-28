# Laudanum, One Webshell to Rule Them All

git clone https://github.com/jbarcia/Web-Shells.git
mkdir -p /usr/share/webshells/laudanum/ 
cp -R Web-Shells/laudanum/ ~/.exegol/workspaces/htb/laudanum/
cp -R /workspace/laudanum/ /usr/share/webshells/laudanum/  

vim /etc/hosts
 10.129.127.238 status.inlanefreight.local

mkdir /home/tester/
cp /usr/share/webshells/Web-Shells/laudanum/aspx/shell.aspx /home/tester/demo.aspx

# Antak Webshell

ls /usr/share/nishang/Antak-WebShell
mkdir -p /home/administrator/ 
cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx

Browser : hstatus.inlanefreight.local\files\Upload.aspx --> http://status.inlanefreight.local/files/Upload.aspx

# PHP Web Shells

