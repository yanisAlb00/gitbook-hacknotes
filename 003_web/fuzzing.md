# Fuzzing

## Ffuf

### Directory fuzzing

```bash
ffuf -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://192.168.207.120/FUZZ/
```

### Extension fuzzing

```
ffuf -w /opt/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ
```

### Page fuzzing

```
ffuf -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ.php
```

### Subdomain fuzzing

```
ffuf -w /opt/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.searcher.htb/                                                                                                        
```

### Vhost fuzzing

```
ffuf -w /opt/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://searcher.htb/ -H 'Host: FUZZ.searcher.htb' -fc 302
```

### Filters

#### Response codes (here 302)

```
-fc 32
```

#### Size

```
-fs 789
```







