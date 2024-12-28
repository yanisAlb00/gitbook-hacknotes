# Kerberos "Double Hop" Problem

A Kerberos authentication is different than a credentials authentication.

In Kerberos cases, tickets are not saved in cache and we can not use it to authenticate to a second server/machine.

## Workaround #1: PSCredential Object

*Evil-WinRM* PS C:\Users\backupadm\Documents> import-module .\PowerView.ps1
*Evil-WinRM* PS C:\Users\backupadm\Documents> klist

-> we only have a cached Kerberos ticket for our current server

$SecPassword = ConvertTo-SecureString '!qazXSW@' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\backupadm', $SecPassword)
get-domainuser -spn -credential $Cred | select samaccountname
get-domainuser -spn | select samaccountname 

RDP to the same host, open a CMD prompt
C:\htb> klist

-> we'll see that we have the necessary tickets cached to interact directly with the Domain Controller, and we don't need to worry about the double hop problem.

## Workaround #2: Register PSSession Configuration

Enter-PSSession -ComputerName ACADEMY-AEN-DEV01.INLANEFREIGHT.LOCAL -Credential inlanefreight\backupadm
klist

-> we only have a cached Kerberos ticket for our current server

Import-Module .\PowerView.ps1
get-domainuser -spn | select samaccountname
Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm
klist

-> we'll see that we have the necessary tickets cached to interact directly with the Domain Controller, and we don't need to worry about the double hop problem.
