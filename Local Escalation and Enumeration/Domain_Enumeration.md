# Domain Enumeration

## Get Information about Current Domain
```powershell
PS C:\AD\Tools> Get-NetDomain
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\Microsoft.ActiveDirectory.Management.dll
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\ActiveDirectory\ActiveDirectory.psd1
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomain
```

## Get Object Information about Another Domain
```powershell
PS C:\AD\Tools> Get-NetDomain -Domain us.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetDomain -Domain moneycorp.local
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomain -Identity moneycorp.local
```

# Domain Controllers

## Get Domain Controllers for the Current Domain
```powershell
PS C:\AD\Tools> Get-NetDomainController
PS C:\AD\Tools\ADModule-master\ADModule-master\ActiveDirectory> Get-ADDomainController
```

## Get Trusted Forest Domain Controller
```powershell
PS C:\AD\Tools> Get-NetDomainController -DomainController moneycorp.local
```

## Get Domain Controllers for Another Domain
```powershell
PS C:\AD\Tools> Get-NetDomainController -Domain moneycorp.local
```

# Domain Policy

## Get Domain Policy for the Current Domain
```powershell
PS C:\AD\Tools> Get-DomainPolicy
PS C:\AD\Tools> (Get-DomainPolicy)."system access"
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```

## Get Kerberos Policy for the Current Domain
```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```

## Get Domain Policy for Another Domain
```powershell
PS C:\AD\Tools> Get-DomainPolicy -Domain moneycorp.local
PS C:\AD\Tools> (Get-DomainPolicy -Domain moneycorp.local)."system access"
```

# Domain SID

## Get Domain SID for the Current Domain and Another Domain
```powershell
PS C:\AD\Tools> Get-DomainSID
PS C:\AD\Tools> Get-DomainSID -Domain moneycorp.local
PS C:\AD\Tools\ADModule-master\ADModule-master> (Get-ADDomain).domainsid
PS C:\AD\Tools\ADModule-master\ADModule-master> (Get-ADDomain -Identity moneycorp.local).domainsid
```

## Get Enterprise Admins Group Data
```powershell
PS C:\AD\Tools\kekeo_old> Get-NetGroup 'enterprise admins' -Domain moneycorp.local -FullData
```


