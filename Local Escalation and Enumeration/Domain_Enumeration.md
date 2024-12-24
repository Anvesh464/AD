### Domain Enumeration

**Get Information about the Current Domain:**

```powershell
PS C:\AD\Tools> Get-NetDomain
```

```powershell
PS C:\AD\Tools> Get-NetDomain -Domain us.dollarcorp.moneycorp.local
```

### Import Modules for AD Management

```powershell
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\Microsoft.ActiveDirectory.Management.dll
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\ActiveDirectory\ActiveDirectory.psd1
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomain
```

### Get Domain Controllers

```powershell
PS C:\AD\Tools> Get-NetDomainController
```

```powershell
PS C:\AD\Tools> Get-NetDomainController -DomainController moneycorp.local
```

```powershell
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomainController
```

```powershell
PS C:\AD\Tools> Get-NetDomainController -Domain moneycorp.local
```

### Domain Policy

**Get Domain Policy for the Current Domain:**

```powershell
PS C:\AD\Tools> Get-DomainPolicy
```

```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."system access"
```

```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
```

```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```

**Get Domain Policy for Another Domain:**

```powershell
PS C:\AD\Tools> Get-DomainPolicy -Domain moneycorp.local
```

```powershell
PS C:\AD\Tools> (Get-DomainPolicy -Domain moneycorp.local)."system access"
```

### Get Domain SID

**Get Domain SID for the Current Domain:**

```powershell
PS C:\AD\Tools> Get-DomainSID
```

**Get Domain SID for Another Domain:**

```powershell
PS C:\AD\Tools> Get-DomainSID -Domain moneycorp.local
