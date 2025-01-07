# Share and File Enumeration in Domain

### Find Shares on Hosts in Current Domain
```powershell
PS C:\AD\Tools> Find-DomainShare –Verbose 
PS C:\AD\Tools> Invoke-ShareFinder
PS C:\AD\Tools> Invoke-ShareFinder -ExcludeStandard -ExcludePrint -ExcludeIPC 
```

### Find Sensitive Files on Computers in the Domain
```powershell
PS C:\AD\Tools> Invoke-FileFinder -Verbose  
```

### Get All Fileservers in the Domain
```powershell
PS C:\AD\Tools> Get-NetFileServer 
```

## Explanation:
- **Find-DomainShare –Verbose**: find interesting shares that is readable and writable by corrent user in the domain
- **Find-DomainShare –Verbose -ExcludeStandard -ExcludeIPC -ExcludePrint**: This command finds shares excluding standard, IPC, and print shares, which could potentially reveal non-standard or hidden shares.
- **Invoke-FileFinder –Verbose**: --> it tries to find sensitive files and computers in the domain. if we have read or write priviledges on share invoke-filefinder tries to look into interesting files into thous shares which contains inresting files like password or username etc...
- **Get-DomainFileServer -Verbose**: it looks for high value target ( lof of user autheticated too) for example domain controller is high value target or file server -- there are highg chances of numer of users to authenticated to file serverr same like go for exchange servers or shared paint servers. thus servers are consider as a high value targets in domain.
```
