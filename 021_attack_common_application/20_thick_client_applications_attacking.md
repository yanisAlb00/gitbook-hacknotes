# Attacking Thick Client Applications

Thick client applications are the applications that are installed locally on our computers.
These applications are usually developed using Java, C++, .NET, or Microsoft Silverlight.

Types of security measures : 

- Java has a technology called sandbox : It isolates untrusted code, preventing it from accessing or modifying system resources and other applications without proper authorization
- Java API restrictions
- Code Signing

Examples : web browsers, media players, chatting software, and video games

2-tier architecture :
Application is installed locally on the computer and communicates directly with the database.

3-tier architecture :
Application is installed locally on the computer, but in order to interact with the databases, they first communicate with an application server, usually using the HTTP/HTTPS protocol.

## Penetration Testing Steps

### Information Gathering

--> Detect programming languages, frameworks and technologies

Examples of tools :
https://ntcore.com/?page_id=388
https://github.com/horsicq/Detect-It-Easy
https://learn.microsoft.com/en-us/sysinternals/downloads/procmon
https://learn.microsoft.com/en-us/sysinternals/downloads/strings

### Client Side attacks

- Identify if there is communication with a remote server

--> Hardcoded credentials and other sensitive information 

Reverse-engineer and examine .NET and Java applications including EXE, DLL, JAR, CLASS, WAR, and other file formats.

Examples of tools :
https://github.com/dnSpy/dnSpy
https://github.com/skylot/jadx

### Network Side Attacks

Network traffic analysis will help us capture sensitive information that might be transferred through HTTP/HTTPS or TCP/UDP connection :

https://www.wireshark.org/
https://www.tcpdump.org/
https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview
https://portswigger.net/burp

### Server Side Attacks

--> Similar to web applications pentesting (OWASP Top Ten)

## Retrieving hardcoded Credentials from Thick-Client Applications

The scenario starts after we have gained access to an exposed SMB service
Exploring the NETLOGON share of the SMB service reveals RestartOracle-Service.exe among other files :

- Download Restart-OracleService.exe locally
- Execute :
.\Restart-OracleService.exe
- If no print, use ProcMon64 from https://learn.microsoft.com/en-gb/sysinternals/downloads/procmon :
- When monitor process, we see that a temp file is created :
C:\Users\Matt\AppData\Local\Temp

- Change the permissions of the Temp folder to disallow file deletions :
- right-click the folder C:\Users\Matt\AppData\Local\Temp
- Properties -> Security -> Advanced -> cybervaca -> Disable inheritance -> Convert inherited permissions into explicit permissions on this object -> Edit -> Show advanced permissions
- deselect the Delete subfolders and files, and Delete checkboxes
- Run again
.\Restart-OracleService.exe
- A file is created : C:\Users\cybervaca\AppData\Local\Temp\2\6F39.bat
- Listing the content of the 6F39 batch

if %username% == matt goto correcto
if %username% == frankytech goto correcto
if %username% == ev4si0n goto correcto
goto error

:correcto
echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt

- Modify the batch script and remove the deletion
- Executing the batch script by double-clicking on it
- Listing content of files
cat C:\programdata\monta.ps1

$salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida))

ls C:\programdata\

Mode                LastWriteTime         Length Name
<SNIP>
-a----        3/24/2023   1:01 PM            273 monta.ps1
-a----        3/24/2023   1:01 PM         601066 oracle.txt
-a----        3/24/2023   1:17 PM         432273 restart-service.exe

.\restart-service.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010

- Inspecting the execution of the executable through ProcMon64

- If nothing solid, Let's start x64dbg, navigate to Options -> Preferences, and uncheck everything except Exit Breakpoint

--> It will avoid going through any dll files

- Then, we can select file -> open and select the restart-service.exe to import it and start the debugging

- Right click inside the CPU view and Follow in Memory Map

--> 0000000000003000 with a type of MAP and protection set to -RW--

- If we double-click on it, we will see the magic bytes MZ in the ASCII column that indicates that the file is a DOS MZ executable

- Right-click on the adress and select Dump Memory to File

- C:\TOOLS\Strings\strings64.exe .\restart-service_00000000001E0000.bin

<SNIP>
"#M
z\V
).NETFramework,Version=v4.0,Profile=Client
FrameworkDisplayName
.NET Framework 4 Client Profile
<SNIP>

--> The dump contains a .NET executable

- Use De4Dot to reverse .NET executables back to the source code :
dragging the restart-service_00000000001E0000.bin onto the de4dot executable

- Read the source code of the exported application
dragging and dropping it onto the DnSpy executable

This binary is a custom-made runas.exe with the sole purpose of restarting the Oracle service using hardcoded credentials

## LAB

xfreerdp /v:10.129.228.115 /u:"cybervaca" /p:"&aue%C)}6g-d{w"

C:\Windows\system32>cd C:\Apps
C:\Apps>.\Restart-OracleService.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010



C:\TOOLS\ProcessMonitor

When we use ProcMon we that a file is created during the execution of the process but not present anymore after running :

CreateFile
CreateFile
C:\Users\cybervaca\AppData\Local\Temp\250D.tmp\250E.tmp\250F.bat

dir C:\Users\cybervaca\AppData\Local\Temp\


Check the folder but subfolders and .bat file are not present anymore.
We will disable the right to delete files for cybervaca user on this folder and run again the .exe : 


- right-click the folder C:\Users\cybervaca\AppData\Local\Temp
- Properties -> Security -> Advanced -> cybervaca -> Disable inheritance -> Convert inherited permissions into explicit permissions on this object -> Edit -> Show advanced permissions
- deselect the Delete subfolders and files, and Delete checkboxes
- Run again
.\Restart-OracleService.exe
- A file is created : C:\Users\cybervaca\AppData\Local\Temp\250D.tmp\250E.tmp\250F.bat
dir C:\Users\cybervaca\AppData\Local\Temp\
- Listing the content of the 6F39 batch

C:\Users\cybervaca\AppData\Local\Temp\BA89.tmp\BA8A.tmp\BA8B.bat

--> Failed because cmd was Run As Administrator while the autorizations were applied on cybervaca

C:\Users\cybervaca>cd C:\Apps

C:\Apps>.\Restart-OracleService.exe

Windows PowerShell terminated with the following error:
 The type initializer for 'System.Management.Automation.Runspaces.InitialSessionState' threw an exception.
'c:\programdata\restart-service.exe' is not recognized as an internal or external command,
operable program or batch file.
Could Not Find c:\programdata\restart-service.exe


dir C:\Users\cybervaca\AppData\Local\Temp\2

 Directory of C:\Users\cybervaca\AppData\Local\Temp\2

01/13/2024  05:38 PM    <DIR>          .
01/13/2024  05:38 PM    <DIR>          ..
01/13/2024  05:37 PM         1,730,212 575E.bat
01/13/2024  05:37 PM                 0 575E.tmp
01/13/2024  05:38 PM    <DIR>          hsperfdata_cybervaca
01/13/2024  05:38 PM             8,376 jusched.log
01/13/2024  05:37 PM                80 __PSScriptPolicyTest_cuaz4mou.msw.ps1
01/13/2024  05:37 PM                80 __PSScriptPolicyTest_yyxrjpcf.4hy.psm1
               5 File(s)      1,738,748 bytes
               3 Dir(s)   2,322,694,144 bytes free

type C:\Users\cybervaca\AppData\Local\Temp\2\575E.bat

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt
c:\programdata\restart-service.exe
del c:\programdata\restart-service.exe

- Modify the batch script and remove the deletion
- Executing the batch script by double-clicking on it
- Listing content of files
dir C:\programdata\

 Volume in drive C has no label.
 Volume Serial Number is 94DB-AFCA

 Directory of C:\programdata

01/13/2024  05:47 PM               273 monta.ps1
03/22/2023  02:11 PM    <DIR>          Oracle
01/13/2024  05:47 PM         1,202,440 oracle.txt
03/22/2023  02:15 PM    <DIR>          Package Cache
01/13/2024  05:32 PM    <DIR>          regid.1991-06.com.microsoft
09/15/2018  08:19 AM    <DIR>          SoftwareDistribution
03/27/2023  10:11 AM    <DIR>          ssh
08/07/2020  11:16 PM    <DIR>          USOPrivate
08/07/2020  11:16 PM    <DIR>          USOShared
03/27/2023  10:53 AM    <DIR>          VMware
               2 File(s)      1,202,713 bytes
               8 Dir(s)   1,833,881,600 bytes free

- Run Powershell as administrator :
PS C:\Windows\system32> cd c:\programdata
PS C:\programdata> .\monta.ps1

.\restart-service.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010

- Inspecting the execution of the executable through ProcMon64

- If nothing solid, Let's start x64dbg, navigate to Options -> Preferences, and uncheck everything except Exit Breakpoint

--> It will avoid going through any dll files

- Then, we can select file -> open and select the restart-service.exe to import it and start the debugging

- Right click inside the CPU view and Follow in Memory Map

--> 0000000000003000 with a type of MAP and protection set to -RW--

- If we double-click on it, we will see the magic bytes MZ in the ASCII column that indicates that the file is a DOS MZ executable

- Right-click on the adress and select Dump Memory to File

C:\restart-service_00007FFC4514D000.bin

- C:\TOOLS\Strings\strings64.exe .\restart-service_00007FFC4514D000.bin

<SNIP>
"#M
z\V
).NETFramework,Version=v4.0,Profile=Client
FrameworkDisplayName
.NET Framework 4 Client Profile
<SNIP>

Strings v2.54 - Search for ANSI and Unicode strings in binary images.
Copyright (C) 1999-2021 Mark Russinovich
Sysinternals - www.sysinternals.com

)`)`*`*`E@E@+`+`

--> The dump contains a .NET executable

- Use De4Dot to reverse .NET executables back to the source code :
dragging the restart-service_00000000001E0000.bin onto the de4dot executable

c:\TOOLS\de4dot>de4dot.exe C:\Users\cybervaca\toto.bin

de4dot v3.1.41592.3405

Detected Unknown Obfuscator (C:\Users\cybervaca\toto.bin)
Cleaning C:\Users\cybervaca\toto.bin
Renaming all obfuscated symbols
Saving C:\Users\cybervaca\toto-cleaned.bin

- Read the source code of the exported application
dragging and dropping it onto the DnSpy executable

This binary is a custom-made runas.exe with the sole purpose of restarting the Oracle service using hardcoded credentials

// runas.Program
// Token: 0x06000001 RID: 1 RVA: 0x00002050 File Offset: 0x00000250
public static void Main(string[] args)
{
	string value = " \r\n    ____            __             __     ____                  __   \r\n   / __ \\___  _____/ /_____ ______/ /_   / __ \\_________ ______/ /__ \r\n  / /_/ / _ \\/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \\\r\n / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/\r\n/_/ |_|\\___/____/\\__/\\__,_/_/   \\__/   \\____/_/   \\__,_/\\___/_/\\___/ \r\n                                                                      \r\n                                                by @HelpDesk 2010\r\n\r\n\t\t\t";
	Console.WriteLine(value);
	Process process = new Process();
	process.StartInfo.FileName = "c:\\windows\\system32\\cmd.exe";
	process.StartInfo.Arguments = "/c sc.exe stop OracleServiceXE; sc.exe start OracleServiceXE";
	process.StartInfo.UserName = "svc_oracle";
	process.StartInfo.UseShellExecute = false;
	SecureString secureString = new SecureString();
	string text = "#oracle_s3rV1c3!2010";
	checked
	{
		for (int i = 0; i < text.Length; i++)
		{
			secureString.AppendChar(text[i]);
		}
		process.StartInfo.Password = secureString;
		process.StartInfo.WindowStyle = ProcessWindowStyle.Hidden;
		process.Start();
	}
}

svc_oracle:#oracle_s3rV1c3!2010