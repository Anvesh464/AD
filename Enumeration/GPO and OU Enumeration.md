# Group Policy Object (GPO) Enumeration

•	**Group Policy Object (GPO)** is a virtual collection of policy settings. A GPO has a unique name, often represented as a GUID. Group policies can be applied to groups, organizational units (OUs), operating systems, computers, or users.</br>
•	Group Policy provides the ability to manage configuration and changes easily and centrally in AD.  </br>
•	Allows configuration of— Security settings, Registry-based policy settings, Group policy preferences like startup/shutdown/log-on/logoff scripts settings, Software installation  </br>
•	GPO can be abused for various attacks like privesc, backdoors,  persistence etc.</br>

### Get all Domain GPOs
```powershell
PS C:\AD\Tools> get-netgpo --> get a list of all group policy in the domain.
PS C:\AD\Tools> Get-NetGPO | select displayname,adspath  displayname adspath ----------- ------- Default Domain Policy  
PS C:\AD\Tools> Get-NetGPO | select displayname  displayname ----------- Default Domain Policy Default Domain Controllers Policy Applocker Servers Students
Get-DomainGPO | Select displayname
```

Group policy applied on Particular Computer
```powershell
PS C:\AD\Tools> Get-NetGPO -ComputerName dcorp-stud157.dollarcorp.moneycorp.local
```
Group policy and OU applied on computer
```powershell
PS C:\AD\Tools> Get-NetOU -OUName Applocked | %{Get-NetComputer -ADSPath $_} --> OU applied on computer (Applocked is the OU)  PS C:\AD\Tools> Get-NetGPO -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local --> GPO applied on computer
```
OU applied On List of computers
```powershell
PS C:\AD\Tools> Get-NetOU -OUName StudentMachines | %{Get-NetComputer -ADSPath $}  PS C:\AD\Tools> Get-NetOU -OUName Applocked | %{Get-NetComputer -ADSPath $} dcorp-adminsrv.dollarcorp.moneycorp.local PS C:\AD\Tools>  PS C:\AD\Tools> Get-NetOU -OUName Servers | %{Get-NetComputer -ADSPath $_} dcorp-sql1.dollarcorp.moneycorp.local dcorp-mgmt.dollarcorp.moneycorp.local
```
- To enumerate Restricted Groups from GPO:
```powershell
PS C:\AD\Tools> Get-NetGPOGroup -Verbose So, no Restricted Groups in our current domain. Now, to look for membership of the Group "RDPUsers” we can use Get-NetGroupMember:
```

# Restricted Group
list of Restricted Group
```powershell
PS C:\AD\Tools> Get-NetGPOGroup --> restricated group polices which are push to group policy on the part of local group on a machine.
```
Get users which are in a local group of a machine using GPO Get users that are part of a Machine's local Admin group
```powershell
PS C:\AD\Tools> Find-GPOComputerAdmin -Computername dcorp-stud157.dollarcorp.moneycorp.local
```
Get machines where the given user is member of a specific group
```powershell
PS C:\AD\Tools> Find-GPOLocation -UserName student157 Verbose
```

### Get a specific GPO by its GUID
```powershell
Get-DomainGPO -Name "{AB306569-220D-43FF-B03B83E8F4EF8081}"
```
# OU
OU
```powershell
PS C:\AD\Tools> Get-NetOU
PS C:\AD\Tools> Get-NetOU -FullData | select name   
PS C:\AD\Tools\ADModule-master\ADModule-master\ActiveDirectory> Get-ADOrganizationalUnit -Filter *
```
Get GPO applied on an particular OU. Read GPOname from gplink attribute from Get-NetOU (Group policy applied studentmachine ou)
```powershell
PS C:\AD\Tools> Get-NetGPO -GPOname '{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}'
```
– List all the computers in the StudentMachines OU.
```powershell
PS C:\AD\Tools> Get-NetOU -OUName StudentMachines LDAP://OU=StudentMachines,DC=dollarcorp,DC=moneycorp,DC=local  PS C:\AD\Tools> Get-NetOU -OUName StudentMachines | %{Get-NetComputer -ADSPath $_}
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
