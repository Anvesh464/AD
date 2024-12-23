# Group Policy Object (GPO) Enumeration

A **Group Policy Object (GPO)** is a virtual collection of policy settings. A GPO has a unique name, often represented as a GUID. Group policies can be applied to groups, organizational units (OUs), operating systems, computers, or users.

### Get all Domain GPOs
```powershell
Get-DomainGPO
```

### Get a list of Domain GPOs and their display names
```powershell
Get-DomainGPO | Select displayname
```

### Get a specific GPO applied to a computer
```powershell
Get-DomainGPO -ComputerName student.pentesting.local
```

### Get machines where a given user is a member of a specific group
```powershell
Get-DomainGPOUserLocalGroupMapping -UserName student1 -Verbose
```

### Get the domain information
```powershell
Get-domain
```

### Enumerate all global catalogs in the forest
```powershell
Get-ForestGlobalCatalog
```

### Get Organizational Units (OUs) in a domain
```powershell
Get-DomainOU
```

### Get the GPOs applied to a specific OU. GPO names are stored in the `gplink` attribute from the Get-NetOU command.
```powershell
Get-DomainGPO
```

### Get a specific GPO by its GUID
```powershell
Get-DomainGPO -Name "{AB306569-220D-43FF-B03B83E8F4EF8081}"
```

## Group Policy Overview

- **Group Policy** provides a centralized way to manage configuration and changes in Active Directory (AD).
- Allows configuration of:
  - **Security settings**
  - **Registry-based policy settings**
  - **Group policy preferences** like startup, shutdown, logon, and logoff script settings
  - **Software installation**
  
## GPO Abuses and Attacks

GPOs can be exploited for various types of attacks, such as:
- **Privilege escalation**
- **Backdoors**
- **Persistence mechanisms**

> **Note**: Misconfigured or poorly managed GPOs can open doors for attackers to exploit administrative privileges or establish persistent access within a network.
```
