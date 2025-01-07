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

## Domain SID
Get domain SID for the current domain and another domain
Get domain SID for the current domain
```powershell
PS C:\AD\Tools> Get-DomainSID   --> Current Domain SID 
PS C:\AD\Tools> Get-DomainSID -Domain moneycorp.local  --> Trusted Domain SID
PS C:\AD\Tools\kekeo_old> Get-NetGroup 'enterprise admins' -Domain moneycorp.local -FullData
```
##################

## Groups (domain and another domain enumeration)
List of Group current domain and forest domain enumeration
```powershell
PS C:\AD\Tools> Get-NetGroup  -->  (This is all are current domain group only not for local groups)
PS C:\AD\Tools> Get-NetGroup -Domain moneycorp.local
PS C:\AD\Tools> Get-NetGroup -FullData ( (which list all the properties every group in the domain)
PS C:\AD\Tools> Get-NetGroup -FullData |select name  --> objectives of groups
PS C:\AD\Tools> Get-NetGroup -GroupName *admin*
PS C:\AD\Tools> Get-NetGroup -GroupName *admin* -Domain moneycorp.local  --> because of extra information got on forest level root required. as a child we can not access.
```
```powershell
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter *
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter * -Properties *
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter * | select name
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter 'name -like "*Admins*"' | select name
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter 'name -like "*admin*"' | select name
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter * | select name
PS C:\AD\Tools\ADModule-master> Get-ADGroup -Filter * | select Name,SID
```
Get a list of domain admin group attributes along with mumbers
```powershell
PS C:\AD\Tools> Get-NetGroup 'domain admins' -FullData  ( gather only one group information on properties) --> domain admin group along with objectives list (or)
PS C:\AD\Tools> PS C:\AD\Tools> Get-NetGroup -GroupName 'domain admins' -FullData
```
```powershell
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter 'name -like "*Admins*"'
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroup -Filter 'name -like "Domain Admins"' -Properties *
```
## Membership of the group  domain admin--> Get all the members of the Domain Admins group and rdpusers
```powershell
PS C:\AD\Tools>  Get-NetGroupMember -GroupName "Domain admins"  --> domain admin group numbers
PS C:\AD\Tools> Get-NetGroupMember "domain admins" | select membern*
PS C:\AD\Tools> Get-NetGroupMember -GroupName "RDPUsers"
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADGroupMember -Identity "Domain admins"
```
# Forest
```powershell
PS C:\AD\Tools> Get-NetGroupMember -GroupName "Domain admins" -Domain moneycorp.local  -->trusted domain admin numbers
PS C:\AD\Tools> Get-NetGroupMember -GroupName 'enterprise admins' -Domain moneycorp.local  --> -->trusted bi-direction of domain admin numbers
PS C:\AD\Tools> Get-NetGroupMember -GroupName "administrators"  --> administrators group numbership (list of users in administrators group)
```
Recurve --enumerate group number ship of the groups
```powershell
PS C:\AD\Tools> Get-NetGroupMember -GroupName "administrators" -Recurse (administrators group numbership (list of users in administrators group along with Objectives)
```

## Get all the groups in the current domain
```powershell
Get-DomainGroup or Get-NetDomain
Get-NetDomain -Domain moneycorp.local   --> Bi-directional trust of any other domain
```
User in Groups
```
PS C:\AD\Tools> Get-NetGroup -UserName student157  --> user is part of below group
PS C:\AD\Tools\ADModule-master\ADModule-master\ActiveDirectory> Get-ADPrincipalGroupMembership -Identity student157
```
# Local Group
List all the local groups on a domain controler (needs administrator privs on non-dc machines) :
```
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -ListGroups  --> local group information of current domain
```
Get members of all the local groups on a machine (needs administrator privs on non dc machines)
```
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Recurse  --> local group information of current domain along with objectives
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

### Get all members of the Enterprise Admins group
```powershell
Get-NetGroupMember -GroupName "Enterprise Admins" -Domain <DomainName>
```

### Get the group membership for a specific user (e.g., student1)
```powershell
Get-DomainGroup –UserName "student1"
```
