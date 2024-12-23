# Share and File Enumeration in Domain

### Find Shares on Hosts in Current Domain
```powershell
Find-DomainShare –Verbose
```

### Find Non-Standard Shares
```powershell
Find-DomainShare –Verbose -ExcludeStandard -ExcludeIPC -ExcludePrint
```

### Find Sensitive Files on Computers in the Domain
```powershell
Invoke-FileFinder –Verbose
```

### Get All Fileservers in the Domain
```powershell
Get-DomainFileServer -Verbose
```

## Explanation:
- **Find-DomainShare –Verbose**: This command finds all shares on hosts in the current domain and provides verbose output.
- **Find-DomainShare –Verbose -ExcludeStandard -ExcludeIPC -ExcludePrint**: This command finds shares excluding standard, IPC, and print shares, which could potentially reveal non-standard or hidden shares.
- **Invoke-FileFinder –Verbose**: This command searches for sensitive files across computers in the domain, providing detailed output.
- **Get-DomainFileServer -Verbose**: This command lists all the file servers in the domain, with verbose output for more detailed information.

By using this format, the commands will appear as clear, executable snippets in your GitHub markdown file, and each section will be clearly separated for better readability and understanding.
```
