# Additional AD Auditing Techniques

## AD Explorer

Creating an AD Snapshot with Active Directory Explorer
go to File --> Create Snapshot

## PingCastle

Viewing the PingCastle Help Menu
PingCastle.exe --help

Running PingCastle
.\PingCastle.exe

PingCastle Interactive TUI :

Select a scanner
================
What scanner whould you like to run ?
WARNING: Checking a lot of workstations may raise security alerts.
  1-aclcheck                                                  9-oxidbindings
  2-antivirus                                                 a-remote
  3-computerversion                                           b-share
  4-foreignusers                                              c-smb
  5-laps_bitlocker                                            d-smb3querynetwork
  6-localadmin                                                e-spooler
  7-nullsession                                               f-startup
  8-nullsession-trust                                         g-zerologon
  0-Exit
==============================
Check authorization related to users or groups. Default to everyone, authenticated users and domain users

--> Viewing The Report

## Group3r

Group3r Basic Usage
group3r.exe -f output.log

type output.log

--> Reading Output

When reading the output from Group3r, each indentation is a different level, so no indent will be the GPO, one indent will be policy settings, and another will be findings in those settings.

## ADRecon

.\ADRecon.ps1

[*] Output Directory: C:\Tools\ADRecon-Report-20220328092458

C:\Tools\ADRecon-Report-20220328092458







## LAB

xfreerdp /u:htb-student /p:"Academy_student_AD!" /v:$TARGET
