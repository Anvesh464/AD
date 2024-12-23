# Domain Computer Enumeration

### Enumerates computers in the current domain with 'outlier' properties
These are properties that are not set according to the forest result returned by `Get-DomainComputer`.

```powershell
Get-DomainComputer -FindOne | Find-DomainObjectPropertyOutlier
```

### Get all domain computers
```powershell
Get-DomainComputer
```

### Get domain computers running Server 2016
```powershell
Get-DomainComputer –OperatingSystem "*Server 2016*"
```

### Get domain computers that respond to a ping
```powershell
Get-DomainComputer -Ping
```

### Get information about a specific domain computer by name
```powershell
Get-DomainComputer -Name "Student.pentesting.local"
```

> **Note**: Older operating systems are often vulnerable, and any services running on such systems can be exploited. Always check for outdated systems and monitor them for potential risks.
```
