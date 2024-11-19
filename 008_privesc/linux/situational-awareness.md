# Situational Awareness

## Basics

### Current User

```
id
whoami
```

### All local users

```
cat /etc/passwd

joe:x:1000:1000:joe,,,:/home/joe:/bin/bash
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
```

x : indicates that the hash password is stored in `/etc/shadow` file

nologin : Used to block any remote or local login for service accounts

### Hostname

```
hostname
```

More often than not, the hostnames will include identifiable abbreviations such as _web_ for a web server, _db_ for a database server, _dc_ for a domain controller, etc.

## Versions

### Opearting System and Kernel Release

```
cat /etc/issue
```

```
cat /etc/os-release
```

```
uname -a
```

## Process

### Current running process

```
ps aux
```
