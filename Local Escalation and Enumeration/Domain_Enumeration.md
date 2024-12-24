# Domain Enumeration

 Get Information about Current Domain
```powershell
PS C:\AD\Tools> Get-NetDomain
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\Microsoft.ActiveDirectory.Management.dll
PS C:\AD\Tools\ADModule-master\ADModule-master> Import-Module .\ActiveDirectory\ActiveDirectory.psd1
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomain
```

 Get Object Information about Another Domain
```powershell
PS C:\AD\Tools> Get-NetDomain -Domain us.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetDomain -Domain moneycorp.local
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADDomain -Identity moneycorp.local
```

# Domain Controllers

 Get Domain Controllers for the Current Domain
```powershell
PS C:\AD\Tools> Get-NetDomainController
PS C:\AD\Tools\ADModule-master\ADModule-master\ActiveDirectory> Get-ADDomainController
```

 Get Trusted Forest Domain Controller
```powershell
PS C:\AD\Tools> Get-NetDomainController -DomainController moneycorp.local
```

 Get Domain Controllers for Another Domain
```powershell
PS C:\AD\Tools> Get-NetDomainController -Domain moneycorp.local
```

# Domain Policy

 Get Domain Policy for the Current Domain
```powershell
PS C:\AD\Tools> Get-DomainPolicy
PS C:\AD\Tools> (Get-DomainPolicy)."system access"
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```

 Get Kerberos Policy for the Current Domain
```powershell
PS C:\AD\Tools> (Get-DomainPolicy)."kerberos policy"
PS C:\AD\Tools> (Get-DomainPolicy)."version"
```

 Get Domain Policy for Another Domain
```powershell
PS C:\AD\Tools> Get-DomainPolicy -Domain moneycorp.local
PS C:\AD\Tools> (Get-DomainPolicy -Domain moneycorp.local)."system access"
```

# Domain SID

 Get Domain SID for the Current Domain and Another Domain
```powershell
PS C:\AD\Tools> Get-DomainSID
PS C:\AD\Tools> Get-DomainSID -Domain moneycorp.local
PS C:\AD\Tools\ADModule-master\ADModule-master> (Get-ADDomain).domainsid
PS C:\AD\Tools\ADModule-master\ADModule-master> (Get-ADDomain -Identity moneycorp.local).domainsid
```

 Get Enterprise Admins Group Data
```powershell
PS C:\AD\Tools\kekeo_old> Get-NetGroup 'enterprise admins' -Domain moneycorp.local -FullData
```
# **User Enumeration**

**Basic Enumeration of a User**

- **Get a list of users in the current domain:**
  ```powershell
  PS C:\AD\Tools> Get-NetUser
  ```

  This will list all users and their default properties in the current domain.

- **Retrieve All Users:**
  ```powershell
  PS C:\AD\Tools> Get-NetUser | select name
  PS C:\AD\Tools> Get-NetUser | select -ExpandProperty samaccountname
  PS C:\AD\Tools> Get-NetUser -Domain moneycorp.local | select cn
  ```

- **Current Domain User List (common name):**
  ```powershell
  PS C:\AD\Tools> Get-NetUser | select cn
  ```

- **Trusted Domain User List:**
  ```powershell
  PS C:\AD\Tools> Get-NetUser -Domain moneycorp.local | select cn
  ```

**Basic Enumeration on a Particular User**

- **Query a specific user:**
  ```powershell
  PS C:\AD\Tools> Get-NetUser -UserName student157
  ```

**ADModule for Specific User**

- **Retrieve User Information with ADModule:**
  ```powershell
  PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Identity student157
  ```

- **Get all properties for a user:**
  ```powershell
  PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Identity student157 -Properties *
  ```

---

# **User Properties**

**Get List of All Properties for Users in the Current Domain**

- **Query for all user properties:**
  ```powershell
  PS C:\AD\Tools> Get-UserProperty
  ```

**Query for Specific Properties**

- **List properties of interest (e.g., `pwdlastset`, `badpasswordtime`, etc.):**
  ```powershell
  PS C:\AD\Tools> Get-UserProperty -Properties pwdlastset
  PS C:\AD\Tools> Get-UserProperty -Properties badpasswordtime
  PS C:\AD\Tools> Get-UserProperty -Properties logoncount
  PS C:\AD\Tools> Get-UserProperty -Properties description
  ```

**Search for Specific String in a User’s Attributes**

- **Search for a term (e.g., `built` in Description):**
  ```powershell
  PS C:\AD\Tools> Find-UserField -SearchField Description -SearchTerm "built"
  ```
```powershell
# List all computers in the domain
Get-NetComputer
```

```powershell
# Get full data for all computers
Get-NetComputer -FullData
```

```powershell
# Filter by operating system (e.g., Windows Server 2016)
Get-NetComputer -OperatingSystem "*Server 2016*"
```

```powershell
# Select specific properties like OperatingSystem and CN
Get-NetComputer -FullData | select operatingsystem,cn | ft or fl
```

```powershell
# Select CN (Common Name) and ObjectSID
Get-NetComputer -FullData | select cn,objectsid
```

```powershell
# Ping the computers to check connectivity
Get-NetComputer -ping
```
# Groups 

```powershell
# List all groups in the current domain
Get-NetGroup
```

```powershell
# List all groups in the domain 'moneycorp.local'
Get-NetGroup -Domain moneycorp.local
```

```powershell
# Get full data for all groups in the domain
Get-NetGroup -FullData
```

```powershell
# Select specific properties of groups in the domain
Get-NetGroup -FullData | select name
```

```powershell
# Filter groups by name, for example, groups with 'admin' in the name
Get-NetGroup -GroupName *admin*
```

```powershell
# Filter groups by name and specify the domain 'moneycorp.local'
Get-NetGroup -GroupName *admin* -Domain moneycorp.local
```

```powershell
# Get full data of the 'Domain Admins' group
Get-NetGroup 'domain admins' -FullData
```

```powershell
# Get the members of the 'Domain Admins' group
Get-NetGroupMember -GroupName "Domain admins"
```

```powershell
# Get the members of the 'Domain Admins' group and show their details
Get-NetGroupMember "domain admins" | select membern*
```

```powershell
# Get the members of the 'RDPUsers' group
Get-NetGroupMember -GroupName "RDPUsers"
```

```powershell
# Get the members of the 'Domain Admins' group from the domain 'moneycorp.local'
Get-NetGroupMember -GroupName "Domain admins" -Domain moneycorp.local
```

```powershell
# Get the members of the 'Enterprise Admins' group from the domain 'moneycorp.local'
Get-NetGroupMember -GroupName 'enterprise admins' -Domain moneycorp.local
```

```powershell
# Recursively enumerate members of the 'Administrators' group
Get-NetGroupMember -GroupName "administrators" -Recurse
```
# Local Group Enumeration

 List All the Local Groups on a Domain Controller
(needs administrator privileges on non-DC machines)

```powershell
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -ListGroups
```

 Get Members of All the Local Groups on a Machine
(needs administrator privileges on non-DC machines)

```powershell
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Recurse
```

These commands will help you enumerate local groups and their members on a domain controller or any specified machine within the domain.
```

This format should make the commands more organized and easier to read and paste into GitHub!
```powershell
# Get the groups that the user 'student157' is a part of
Get-NetGroup -UserName student157
``` 
# Local Group Enumeration

 List All the Local Groups on a Domain Controller
(needs administrator privileges on non-DC machines)

```powershell
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -ListGroups
```

 Get Members of All the Local Groups on a Machine
(needs administrator privileges on non-DC machines)

```powershell
PS C:\AD\Tools> Get-NetLocalGroup -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Recurse
```

These commands will help you enumerate local groups and their members on a domain controller or any specified machine within the domain.

# Logged On Users on Machine

 Get a List of Actively Logged On Users in a Computer
```powershell
PS C:\AD\Tools> Get-NetLoggedon  # local machine
PS C:\AD\Tools> Get-NetLoggedon -ComputerName dcorp-dc.dollarcorp.moneycorp.local  # given host for actively logged on users
```

First, find local admin access. Afterward, use the computer name to find who is logged on to the system, so you can dump hashes for that machine.

 Get Locally Logged Users on a Computer
(needs remote registry on the target started by default on server OS)

```powershell
PS C:\AD\Tools> Get-LoggedonLocal -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

 Example Output
```plaintext
ComputerName                        UserDomain UserName      UserSID
------------                        ---------- --------      -------
dcorp-dc.dollarcorp.moneycorp.local dcorp      svcadmin      S-1-5-21-1874506631-3219952063-538504511-1122
dcorp-dc.dollarcorp.moneycorp.local dcorp      Administrator S-1-5-21-1874506631-3219952063-538504511-500
```

 Get the Last Logged User on a Computer
(needs administrative rights and remote registry on the target)

```powershell
PS C:\AD\Tools> Get-LastLoggedOn -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```
# Shares Enumeration

 Find Shares on Hosts in Current Domain
```powershell
PS C:\AD\Tools> Invoke-ShareFinder -Verbose
```

```powershell
PS C:\AD\Tools> Invoke-ShareFinder
```

 Find Specific Shares (Excluding Standard, Print, and IPC)
```powershell
PS C:\AD\Tools> Invoke-ShareFinder -ExcludeStandard -ExcludePrint -ExcludeIPC
```

# Sensitive Files on Computers in the Domain

 Find Sensitive Files on Computers in the Domain
```powershell
PS C:\AD\Tools> Invoke-FileFinder -Verbose
```

# File Servers in the Domain

 Get All File Servers of the Domain
```powershell
PS C:\AD\Tools> Get-NetFileServer
```
# Group Policy Enumeration

 List All Group Policies in the Domain
```powershell
PS C:\AD\Tools> Get-NetGPO
```

 Display Group Policies with Display Name and AD Path
```powershell
PS C:\AD\Tools> Get-NetGPO | select displayname,adspath
```

 Get Details of a Specific Group Policy
```powershell
PS C:\AD\Tools> Get-NetGPO -ADSpath 'LDAP://cn={3E04167E-C2B6-4A9A-8FB7-C811158DC97C},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local'
```

# Group Policy Applied on a Particular Computer

 Get Group Policy Applied on a Specific Computer
```powershell
PS C:\AD\Tools> Get-NetGPO -ComputerName dcorp-stud157.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetGPO -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

# Group Policy and OU Applied on Computers

 Get OU Applied on Computers
```powershell
PS C:\AD\Tools> Get-NetOU -OUName Applocked  | %{Get-NetComputer -ADSPath $_}
PS C:\AD\Tools> Get-NetOU -OUName StudentMachines  | %{Get-NetComputer -ADSPath $_}
PS C:\AD\Tools> Get-NetOU -OUName Servers  | %{Get-NetComputer -ADSPath $_}
```

 Get Group Policy Applied on a Specific Computer
```powershell
PS C:\AD\Tools> Get-NetGPO -ComputerName dcorp-adminsrv.dollarcorp.moneycorp.local
```

# Enumerate Restricted Groups from GPO

 Get All Restricted Groups
```powershell
PS C:\AD\Tools> Get-NetGPOGroup -Verbose
```

# Restricted Group Enumeration

 List of Restricted Groups
```powershell
PS C:\AD\Tools> Get-NetGPOGroup
```

 Get Users in a Local Group of a Machine Using GPO
Get users that are part of a Machine's local Admin group.
```powershell
PS C:\AD\Tools> Find-GPOComputerAdmin -Computername dcorp-stud157.dollarcorp.moneycorp.local
```

 Get Machines Where the Given User is a Member of a Specific Group
```powershell
PS C:\AD\Tools> Find-GPOLocation -UserName student157 -Verbose
```

# Organizational Units (OUs)

 List All OUs with Full Data
```powershell
PS C:\AD\Tools> Get-NetOU -FullData | select name
PS C:\AD\Tools> Get-NetOU
```

 Get Detailed Information of OUs
```powershell
PS C:\AD\Tools> Get-NetOU -FullData
```

# Get Group Policy Applied on a Particular OU

 Read GPO Name from gplink Attribute
```powershell
PS C:\AD\Tools> Get-NetGPO -GPOname '{3E04167E-C2B6-4A9A-8FB7-C811158DC97C}'
```

# List All Computers in the StudentMachines OU

 Get OUs and List Computers
```powershell
PS C:\AD\Tools> Get-NetOU -OUName StudentMachines
PS C:\AD\Tools> Get-NetOU -OUName StudentMachines | %{Get-NetComputer -ADSPath $_}
```

# Access Control Models (ACL)

 Get the ACLs Associated with the Specified Object (student157)
```powershell
PS C:\AD\Tools> Get-ObjectAcl -SamAccountName student157 -ResolveGUIDs
```

 Get the ACLs Associated with the Specified Prefix
```powershell
PS C:\AD\Tools> Get-ObjectAcl -ADSprefix 'CN=Administrator,CN=Users' -Verbose
```

 Get the ACLs Associated with the Specified LDAP Path (groups)
```powershell
PS C:\AD\Tools> Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose
```

# User Details

 Get User Details
```powershell
PS C:\AD\Tools> Get-ADUser -Identity student157
```

# ACL for Groups

 Get ACL for the Users Group
```powershell
PS C:\AD\Tools> Get-ObjectAcl -SamAccountName "users" –ResolveGUIDs
```

 Get ACL for the Domain Admins Group
```powershell
PS C:\AD\Tools> Get-ObjectAcl -SamAccountName "Domain Admins" –ResolveGUIDs
```

 Enumerate All Modify Rights/Permissions for student157
```powershell
PS C:\AD\Tools> Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name}
PS C:\AD\Tools> Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name} | ?{$_.IdentityReference -match "student"}
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "dcorp\student157"}
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "student157"}
PS C:\AD\Tools> Get-NetGroupMember -GroupName rdpusers
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "rdpusers"}
```

# Search for Interesting ACEs

 Invoke ACL Scanner
```powershell
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs
```

# Get the ACLs Associated with the Specified Path

 Get Path ACL
```powershell
PS C:\AD\Tools> Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"
```

# Domain Trust

 Get a List of All Domain Trusts for the Current Domain
(Map the trusts of the `dollarcorp.moneycorp.local` domain. To map all the trusts of the `moneycorp.local` forest:)

```powershell
PS C:\AD\Tools> Get-NetDomainTrust
PS C:\AD\Tools> Get-NetDomainTrust -Domain moneycorp.local
PS C:\AD\Tools> Get-NetDomainTrust -Domain us.dollarcorp.moneycorp.local
PS C:\AD\Tools> Get-NetDomainTrust -Domain eurocorp.local
```

# Enumerate Trust on Child or Parent Domain

```powershell
PS C:\AD\Tools> Get-NetDomainTrust -Domain us.dollarcorp.moneycorp.local
```

# Forest Trust

 Get Details about the Current Forest
```powershell
PS C:\AD\Tools> Get-NetForest
```

 Get Details about Another Forest
```powershell
PS C:\AD\Tools> Get-NetForest -Forest eurocorp.local
```

# Get All Domains in the Current Forest

```powershell
PS C:\AD\Tools> Get-NetForestDomain | ft
PS C:\AD\Tools> Get-NetForestDomain
```

# Forest Mapping

 Map Trusts of a Forest
```powershell
PS C:\AD\Tools> Get-NetForestDomain -Forest eurocorp.local
PS C:\AD\Tools> Get-NetForestTrust -Forest eurocorp.local
PS C:\AD\Tools> Get-NetDomainTrust
```

 Get Some Information from External Forest
```powershell
PS C:\AD\Tools> Get-NetUser -Domain eurocorp.local
```

 Get All Global Catalogs for the Current Forest
```powershell
PS C:\AD\Tools> Get-NetForestCatalog
PS C:\AD\Tools> Get-NetForestCatalog -Forest eurocorp.local
```

 Map External Trusts in moneycorp.local Forest
```powershell
PS C:\AD\Tools> Get-NetForestDomain -Verbose | Get-NetDomainTrust
PS C:\AD\Tools> Get-NetForestDomain -Verbose | Get-NetDomainTrust | ?{$_.TrustType -eq 'external'}
```

 Enumerate Trusts for eurocorp.local Forest
```powershell
PS C:\AD\Tools> Get-NetForest -Forest eurocorp.local -Verbose | Get-NetDomainTrust
```
