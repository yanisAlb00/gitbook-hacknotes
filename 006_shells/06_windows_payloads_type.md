## Payload Types to Consider

DLLs : Inject malicious DLL which are used by many different programs
Batch : files with .bat extension used by administrators to automate some commands
VBS : Scripting language based on Microsoft Visual Basic (disabled on most of websites) but still use by phishing aimed at having users perform an action such as enable loading of macros
MSI : .msi file is a installation database for Windows installer. Execute by msiexec
Powershell : Both shell environment and scripting language

## Payload Transfer and execution

Impacket : interact with network protocols directly (deal with psexec, smbclient, wmi, Kerberos, and the ability to stand up an SMB server)
Payloads All The Things : find quick oneliners to help transfer files across hosts expediently
SMB : Transfer files between hosts
Remote execution via MSF (Metasploit)
Other Protocols : FTP,TFTP, HTTP,...

## CMD vs PowerShell

Use CMD when:
- You are on an older host that may not include PowerShell.
- When you only require simple interactions/access to the host.
- When you plan to use simple batch files, net commands, or MS-DOS native tools.
- When you believe that execution policies may affect your ability to run scripts or other actions on the host.

Use PowerShell when:
- You are planning to utilize cmdlets or other custom-built scripts.
- When you wish to interact with .NET objects instead of text output.
- When being stealthy is of lesser concern.
- If you are planning to interact with cloud-based services and hosts.
- If your scripts set and use Aliases.



