# Domain Computer Enumeration

### Enumerates computers in the current domain with 'outlier' properties
These are properties that are not set according to the forest result returned by `Get-DomainComputer`.

```powershell
Get-DomainComputer -FindOne | Find-DomainObjectPropertyOutlier
```

### Get all domain computers
```powershell
Get-DomainComputer
PS C:\AD\Tools> Get-NetComputer  -->gives list of all computers in current domain
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer  -->current domain computers list
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer -Filter * -Properties *  --
```

### Get domain computers running Server 2016
```powershell
Get-DomainComputer –OperatingSystem "*Server 2016*"
PS C:\AD\Tools> Get-NetComputer -OperatingSystem ""*Server 2016*""
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer -Filter 'OperatingSystem -like ""*Server 2016*""' -Properties OperatingSystem | select Name,OperatingSystem
```

### Get domain computers that respond to a ping
```powershell
Get-DomainComputer -Ping
PS C:\AD\Tools> Get-NetComputer -ping
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
```

### Get information about a specific domain computer by name
```powershell
Get-DomainComputer -Name "Student.pentesting.local"
```
Get a list of computers in the current domain
```powershell
PS C:\AD\Tools> Get-NetComputer  -->gives list of all computers in current domain
PS C:\AD\Tools> Get-NetComputer -FullData  --> current domain computer list along with an objectives
PS C:\AD\Tools> Get-NetComputer -OperatingSystem "*Server 2016*"

PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer  -->current domain computers list
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer -Filter * -Properties *  --
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADComputer -Filter 'OperatingSystem -like "*Server 2016*"' -Properties OperatingSystem | select Name,OperatingSystem
```
### Another way filtering
```powershell
PS C:\Users\PoCsecur> Get-ADUser -Filter {Enabled -eq $false} -Properties Enabled | select SamAccountName,Enabled >AD_Userlist2.txt
PS C:\Users\PoCsecur> Get-ADUser -Filter {Enabled -eq $true} -Properties Enabled | select SamAccountName,Enabled >Active_User_List.txt
PS C:\Users\PoCsecur> Get-ADComputer -Filter {Enabled -eq $true} -Properties Enabled | Select-Object Name,Enabled >Computers_list.txt
PS C:\Users\PoCsecur\Desktop> Get-ADComputer -Filter 'OperatingSystem -like "*Server*"' -Properties OperatingSystem | select name,Enabled,OperatingSystem >serverlist.txt
C:\AD\Tools> Get-NetComputer -FullData  --> current domain computer list along with an objectives
PS C:\AD\Tools> Get-NetComputer -FullData | select cn,objectsid
PS C:\AD\Tools> Get-NetComputer -FullData | select operatingsystem,cn | ft or fl
```
