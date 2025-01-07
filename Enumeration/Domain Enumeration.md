# Domain Group and User Enumeration
## Domain Enumeration

Get Information about current domain
```powershell
PS C:\AD\Tools> Get-NetDomain
PS C:\AD\Tools> Get-NetDomain -Domain us.dollarcorp.moneycorp.local
```
Get object of another domain
```powershell
PS C:\AD\Tools> Get-NetDomain -Domain  moneycorp.local   --> Bi-directional trust of any other domain
```

## Domain Controller
Get domain controllers for the current domain
```powershell
PS C:\AD\Tools> Get-NetDomainController   --> AD of current Domain
PS C:\AD\Tools> Get-NetDomainController -DomainController  moneycorp.local  -- trusted Forest Domain Controller
```
Get domain controllers for another domain
```powershell
PS C:\AD\Tools> Get-NetDomainController -Domain moneycorp.local
```

## Domain Policy
Get domain policy for the current domain
```powershell
PS C:\AD\Tools> Get-DomainPolicy   --> Current Domain Policy Checks
PS C:\AD\Tools> (Get-DomainPolicy)."system access"
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"

```
Get Krbtgt policy for the current domain
```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```
Get domain policy for another domain
```powershell
PS C:\AD\Tools> Get-NetDomainController -Domain moneycorp.local
PS C:\AD\Tools> Get-DomainPolicy -Domain moneycorp.local   --> Trusted Domain Policy Checks
PS C:\AD\Tools> (Get-DomainPolicy -Domain moneycorp.local)."system access"
```

## Get all the groups in the current domain
```powershell
Get-DomainGroup or Get-NetDomain
Get-NetDomain -Domain moneycorp.local   --> Bi-directional trust of any other domain
```
## Domain SID
Get domain SID for the current domain
```powershell
PS C:\AD\Tools> Get-DomainSID   --> Current Domain SID 
PS C:\AD\Tools> Get-DomainSID -Domain moneycorp.local  --> Trusted Domain SID
PS C:\AD\Tools\kekeo_old> Get-NetGroup 'enterprise admins' -Domain moneycorp.local -FullData
```

## Get the members of a specific group (e.g., Domain Admins)
```powershell
Get-DomainGroupMember -Name "Domain Admins"
```
![image](https://github.com/user-attachments/assets/5ba93be5-e1ee-4c3c-a178-ffca50c10a6e)

![image](https://github.com/user-attachments/assets/8a55927f-ee77-4471-8918-04f834f2146c)

### Enumerate groups in a different domain

```powershell
Get-DomainGroup –Domain <targetdomain>
```

### Get the members of the Domain Admins group in a different domain
```powershell
Get-DomainGroupMember -Name "Domain Admins" -Domain "domain name"
```

### Get all the members of the Domain Admins group
```powershell
Get-NetGroupMember -GroupName "Domain Admins"
```

### Recursively get all members of the Domain Admins group
```powershell
Get-NetGroupMember -GroupName "Domain Admins" -Recurse
```

### Get all Organizational Units (OUs)
```powershell
Get-DomainOU
```

### Get all members of the Enterprise Admins group
```powershell
Get-NetGroupMember -GroupName "Enterprise Admins" -Domain <DomainName>
```

### Get the group membership for a specific user (e.g., student1)
```powershell
Get-DomainGroup –UserName "student1"
```

### Recursively get group members of Domain Admins
```powershell
Get-NetGroupMember -GroupName "Domain Admins" -Recurse
```
