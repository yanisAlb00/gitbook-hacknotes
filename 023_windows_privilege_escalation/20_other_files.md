# Other Files

In an Active Directory environment : we can use tool such as https://github.com/SnaffCon/Snaffler

It will crawl network share drives for interesting file extensions such as .kdbx, .vmdk, .vdhx, .ppk, etc

## Manually Searching the File System for Credentials

### Search File Contents with CMD

Example 1 :

```
cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt

stuff.txt
```

Example 2 :

```
findstr /si password *.xml *.ini *.txt *.config

stuff.txt:password: l#-x9r11_2_GL!
```

Example 3 :

```
findstr /spin "password" *.*

stuff.txt:1:password: l#-x9r11_2_GL!
```

### Search File Contents with PowerShell

```
select-string -Path C:\Users\htb-student\Documents\*.txt -Pattern password

stuff.txt:1:password: l#-x9r11_2_GL!
```

### Search for File Extensions using CMD

Search for File Extensions - Example 1 :

```
dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*

c:\inetpub\wwwroot\web.config
```

Search for File Extensions - Example 2 :

```
where /R C:\ *.config

c:\inetpub\wwwroot\web.config
```

### Search for File Extensions Using PowerShell


```
Get-ChildItem C:\ -Recurse -Include *.rdp, *.config, *.vnc, *.cred -ErrorAction Ignore


    Directory: C:\inetpub\wwwroot


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021   9:59 AM            329 web.config

<SNIP>
```

## Sticky Notes Passwords

### Looking for StickyNotes DB Files

```
PS C:\htb> ls
 
 
    Directory: C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState
 
 
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021  11:59 AM          20480 15cbbc93e90a4d56bf8d9a29305b8981.storage.session
-a----         5/25/2021  11:59 AM            982 Ecs.dat
-a----         5/25/2021  11:59 AM           4096 plum.sqlite
-a----         5/25/2021  11:59 AM          32768 plum.sqlite-shm
-a----         5/25/2021  12:00 PM         197792 plum.sqlite-wal
```

```
where /R C:\ *.sqllite
```

```
Get-ChildItem C:\ -Recurse -Include *.sqlite -ErrorAction Ignore
```

--> copy the three plum.sqlite* files down to our system and open them with a tool such as DB Browser for SQLite and view the Text column in the Note table with the query select Text from Note;

https://sqlitebrowser.org/dl/

### Viewing Sticky Notes Data Using PowerShell

https://github.com/RamblingCookieMonster/PSSQLite

```
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A

PS C:\htb> cd .\PSSQLite\
PS C:\htb> Import-Module .\PSSQLite.psd1
PS C:\htb> $db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
PS C:\htb> Invoke-SqliteQuery -Database $db -Query "SELECT Text FROM Note" | ft -wrap
 
Text
----
\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
\id=e4adae4c-a40b-48b4-93a5-900247852f96
\id=1a44a631-6fff-4961-a4df-27898e9e1e65 root:Vc3nt3R_adm1n!
\id=c450fc5f-dc51-4412-b4ac-321fd41c522a Thycotic demo tomorrow at 10am
```

### Strings to View DB File Contents on our linux attack box


```
strings plum.sqlite-wal

CREATE TABLE "Note" (
"Text" varchar ,
"WindowPosition" varchar ,
"IsOpen" integer ,
"IsAlwaysOnTop" integer ,
"CreationNoteIdAnchor" varchar ,
"Theme" varchar ,
"IsFutureNote" integer ,
"RemoteId" varchar ,
"ChangeKey" varchar ,
"LastServerVersion" varchar ,
"RemoteSchemaVersion" integer ,
"IsRemoteDataInvalid" integer ,
"PendingInsightsScan" integer ,
"Type" varchar ,
"Id" varchar primary key not null ,
"ParentId" varchar ,
"CreatedAt" bigint ,
"DeletedAt" bigint ,
"UpdatedAt" bigint )'
indexsqlite_autoindex_Note_1Note
af907b1b-1eef-4d29-b238-3ea74f7ffe5caf907b1b-1eef-4d29-b238-3ea74f7ffe5c
U	af907b1b-1eef-4d29-b238-3ea74f7ffe5c
Yellow93b49900-6530-42e0-b35c-2663989ae4b3af907b1b-1eef-4d29-b238-3ea74f7ffe5c
U	93b49900-6530-42e0-b35c-2663989ae4b3


< SNIP >

\id=011f29a4-e37f-451d-967e-c42b818473c2 vCenter
\id=34910533-ddcf-4ac4-b8ed-3d1f10be9e61 alright*
\id=ffaea2ff-b4fc-4a14-a431-998dc833208c root:Vc3nt3R_adm1n!ManagedPosition=Yellow93b49900-6530-42e0-b35c-2663989ae4b3af907b1b-1eef-4d29-b238-3ea74f7ffe5c

<SNIP >
```

## Other Files of Interest

Some other files we may find credentials in include the following:

%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*

## LAB

xfreerdp /v:10.129.43.44 /u:htb-student /p:HTB_@cademy_stdnt!

C:\Users\htb-student>cd c:\Users\htb-student\Documents & findstr /SI /M "password" *.xml *.ini *.txt
stuff.txt

c:\Users\htb-student\Documents>findstr /si password *.xml *.ini *.txt *.config
stuff.txt:password: l#-x9r11_2_GL!

c:\Users>dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
c:\Users\All Users\Microsoft\UEV\InboxTemplates\RoamingCredentialSettings.xml
c:\Users\htb-student\AppData\Local\Google\Chrome\User Data\ZxcvbnData\1\passwords.txt
c:\Users\htb-student\AppData\Local\Packages\Microsoft.CredDialogHost_cw5n1h2txyewy
c:\Users\htb-student\AppData\Local\Packages\windows.immersivecontrolpanel_cw5n1h2txyewy\LocalState\Indexed\Settings\en-US\AAA_SystemSettings_SyncSettings_SyncCredentials_Toggle.settingcontent-ms
c:\Users\htb-student\AppData\Roaming\Microsoft\Windows\Recent\powershell.exe.config.lnk
c:\Users\htb-student\AppData\Roaming\Microsoft\Windows\Recent\powershell.exe.config.txt.lnk
c:\Users\htb-student\AppData\Roaming\Microsoft\Windows\Recent\web.config.lnk

c:\Users>where /R C:\ *.config
C:\inetpub\wwwroot\web.config
C:\Program Files\GVFS\GVFS.exe.config
C:\Program Files\GVFS\GVFS.Hooks.exe.config
C:\Program Files\GVFS\GVFS.Mount.exe.config
...

PS C:\Users> type c:\inetpub\wwwroot\web.config
<?xml version='1.0' encoding='utf-8'?>
<configuration>
  <connectionStrings>
  <add
    name="sqlServer"
    providerName="System.Data.SqlClient"
    connectionString="Data Source=localhost;Initial Catalog=DB01.INLANEFREIGHT.LOCAL;User Id=sa;Password=W1ck3d_g00d_Db_P@ss!;" />
</connectionStrings>>
</configuration>

Get-ChildItem C:\ -Recurse -Include *.sqlite -ErrorAction Ignore

PS C:\Users> Get-ChildItem C:\ -Recurse -Include *.sqlite -ErrorAction Ignore


    Directory: C:\Tools\PSSQLite-master\Tests


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021  12:03 PM           3072 Names.SQLite

PS C:\Users> dir C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState


    Directory: C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         5/25/2021  12:19 PM          20480 15cbbc93e90a4d56bf8d9a29305b8981.storage.session
-a----         5/25/2021  11:59 AM            982 Ecs.dat
-a----         5/25/2021  12:20 PM          77824 plum.sqlite

PS C:\Users> dir C:\Users\bob\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState
dir : Access is denied

PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A

Set-ExecutionPolicy : Windows PowerShell updated your execution policy successfully, but the setting is overridden by
a policy defined at a more specific scope.

PS C:\Tools> cd .\PSSQLite\
PS C:\Tools\PSSQLite> $db = 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'
PS C:\Tools\PSSQLite> Import-Module .\PSSQLite.psd1

Security warning
Run only scripts that you trust. While scripts from the internet can be useful, this script can potentially harm your
computer. If you trust this script, use the Unblock-File cmdlet to allow the script to run without this warning
message. Do you want to run C:\Tools\PSSQLite\PSSQLite.psm1?
[D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"): D
Import-Module : File C:\Tools\PSSQLite\PSSQLite.psm1 cannot be loaded because you opted not to run this software now.
At line:1 char:1
+ Import-Module .\PSSQLite.psd1
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : SecurityError: (:) [Import-Module], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.PowerShell.Commands.ImportModuleCommand

--> Failed

type 'C:\Users\htb-student\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite'

š♀{☻↨‚?‚#     UU♠♠\id=e30f6663-29fa-465e-895c-b031e061a26a Network
\id=c73f29c3-64f8-4cfc-9421-f65c34b4c00e
\id=69b4fc18-ae09-4226-af90-175ff4092b79 bob_adm:1qazXSW@3edc!ManagedPosition=DeviceId:\\?\DISPLAY#Default_Monitor#4&31be19fa&0&UID0‚∟♥§‚?-   UU♠♠\id=e30f6663-29fa-465e-895c-b031e061a26a Network
\id=c73f29c3-64f8-4cfc-9421-f65c34b4c00e
\id=69b4fc18-ae09-4226-af90-175ff4092b79 bob_adm:1qazXSW@3edc!ManagedPosition=Yellow6d42fa55-4fa6-4799-80f0-015b79cf7a3135388d4f-b79d-48b3-b5de-eb0c7f88d82Ù▼²☼ñ☻Ù▼²1†àtJU♠♠\id=c450fc5f-dc51-4412-b4ac-321fd41c522a Thycotic demo tomorrow at 10amManagedPosition=Yellow6268cfc8-39b7-4970-9ead-f9113370195435388d4f-b79d-48b3-b5de-eb0c7f88d82Ù▼¯W☻üÙ▼¯[²¡Ý|‚;‚#  UU♠\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
\id=e4adae4c-a40b-48b4-93a5-900247852f96
\id=1a44‚→☺§‚;UU♠♠\id=de368df0-6939-4579-8d38-0fda521c9bc4 vCenter
\id=e4adae4c-a40b-48b4-93a5-900247852f96
\id=1a44a631-6fff-4961-a4df-27898e9e1e65 root:Vc3nt3R_adm1n!ManagedPosition=Yellow5d055d93-6b75-4853-a613-43b2370e4bbb35388d4f-b79d-48b3-b5de-eb0c7f88d82Ù▼¯D‚ÏÙ▼¯UkEr
♥☼†☼Ø☼¯☼†(♥U☺6d42fa55-4fa6-4799-80f0-015b79cf7a31♥(♥U☺6268cfc8-39b7-4970-9ead-f91133701954☻'♥U  5d055d93-6b75-4853-a613-43b2370e4bbb