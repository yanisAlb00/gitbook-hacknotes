# Attacking Applications Connecting to Services

Applications that are connected to services often include connection strings that can be leaked if they are not protected sufficiently.

## ELF Executable Examination

The octopus_checker binary is found on a remote machine during the testing :
./octopus_checker 

Program had started..
Attempting Connection 
Connecting ... 

The driver reported the following diagnostics whilst running SQLDriverConnect

01000:1:0:[unixODBC][Driver Manager]Can't open lib 'ODBC Driver 17 for SQL Server' : file not found
connected

--> The binary probably connects using a SQL connection string that contains credentials

https://github.com/longld/peda
gdb ./octopus_checker

--> Identify where the SQL parameters could be passed and add strategical breakpoints :

gdb-peda$ set disassembly-flavor intel
gdb-peda$ disas main

   0x0000555555555607 <+433>:	call   0x5555555551b0 <SQLDriverConnect@plt>


gdb-peda$ b *0x5555555551b0
gdb-peda$ run

Starting program: /htb/rollout/octopus_checker 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Program had started..
Attempting Connection 
[----------------------------------registers-----------------------------------]
RAX: 0x55555556c4f0 --> 0x4b5a ('ZK')
RBX: 0x0 
RCX: 0xfffffffd 
RDX: 0x7fffffffda70 ("DRIVER={ODBC Driver 17 for SQL Server};SERVER=localhost, 1401;UID=username;PWD=password;")
RSI: 0x0 
RDI: 0x55555556c4f0 --> 0x4b5a ('ZK')

<SNIP>

--> Try to reuse password

## DLL File Examination

The MultimasterAPI.dll binary is found on a remote machine during the enumeration process. Examination of the file reveals that this is a .Net assembly :

Get-FileMetaData .\MultimasterAPI.dll

<SNIP>
M .NETFramework,Version=v4.6.1 TFrameworkDisplayName.NET Framework 4.6.1    api/getColleagues        ! htt
p://localhost:8081*POST         Ò^         øJ  ø,  RSDSœ»¡ÍuqœK£"Y¿bˆ   C:\Users\Hazard\Desktop\Stuff\Multimast
<SNIP>

--> Use the debbuger and https://github.com/0xd4d/dnSpy

Inspection of MultimasterAPI.Controllers -> ColleagueController reveals a database connection string containing the password

## LAB

ssh htb-student@10.129.205.20
HTB_@cademy_stdnt!

./octopus_checker 
Program had started..
Attempting Connection 
Connecting ... 

The driver reported the following diagnostics whilst running SQLDriverConnect

01000:1:5701:[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Changed database context to 'master'.
01000:2:5703:[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]Changed language setting to us_english.
connected

gdb ./octopus_checker
set disassembly-flavor intel
gdb-peda$ disas main

   0x0000000000001607 <+433>:	call   0x11b0 <SQLDriverConnect@plt>

gdb-peda$ b *0x11b0
Breakpoint 1 at 0x11b0
gdb-peda$ run
Starting program: /home/htb-student/octopus_checker 
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0x11b0

--> Failed

Try in a different order :
gdb ./octopus_checker
gdb-peda$ b *0x11b0
gdb-peda$ run
gdb-peda$ set disassembly intel
gdb-peda$ type disas main


   0x0000555555555607 <+433>:	call   0x5555555551b0 <SQLDriverConnect@plt>


--> Exit and :

gdb-peda$ b *0x5555555551b0
Breakpoint 2 at 0x5555555551b0
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0x11b0

gdb-peda$ run
Starting program: /home/htb-student/octopus_checker 
Warning:
Cannot insert breakpoint 1.
Cannot access memory at address 0x11b0

gdb-peda$ Aborted (core dumped)
htb-student@htb:~$ gdb ./octopus_checker

RDX: 0x7fffffffde40 ("DRIVER={ODBC Driver 17 for SQL Server};SERVER=localhost, 1401;UID=SA;PWD=N0tS3cr3t!;")


b *0x5555555551b0

SA:N0tS3cr3t!