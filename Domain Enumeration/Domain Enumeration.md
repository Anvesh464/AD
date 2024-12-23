# Domain Group and User Enumeration

### Get all the groups in the current domain
```powershell
Get-DomainGroup
```

### Get the members of a specific group (e.g., Domain Admins)
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
