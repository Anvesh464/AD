# Access control Models(ACL)  --> users and group Every Organization implemented ACL auditing regularly

Get the ACLs associated with the specified object (student157)
``` powershell
PS C:\AD\Tools> Get-ObjectAcl -SamAccountName student157 -ResolveGUIDs   --> get a list of interesting acls for the student157

3 importance things
ObjectDN(Target user), Identify reference (where), AccessControlType (ActiveDirectoryRights- target rights)	
PS C:\AD\Tools\ADModule-master\ADModule-master\ActiveDirectory> (Get-Acl 'AD:\CN=Administrator,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local').Access
```

Get the ACLs associated with the specified prefix to be used for search	
```
PS C:\AD\Tools> Get-ObjectAcl -ADSprefix 'CN=Administrator,CN=Users' -Verbose   ( we can search group)
```
Get the ACLs associated with the specified LDAP path to be used for search (groups)	
```
PS C:\AD\Tools> Get-ObjectAcl -ADSpath "LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local" -ResolveGUIDs -Verbose
– User details	PS C:\AD\Tools> Get-ADUser -Identity student157
– ACL for the Users group	Get-ObjectAcl -SamAccountName "users" –ResolveGUIDs   -->  ACL for the Users group ( beside output)
– ACL for the Domain Admins group	Get-ObjectAcl -SamAccountName "Domain Admins" –ResolveGUIDs  --> ACL for the Domain Admins group
– All modify rights/permissions for the student157
```
```
Let’s enumerate ACLs for all the GPOs.
PS C:\AD\Tools> Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name}
```

Now, to enumerate those GPOs where studentx or RDPUsers group have interesting permissions:
```
PS C:\AD\Tools> Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name} | ?{$_.IdentityReference -match "student"}
```
3.  PS C:\AD\Tools> Invoke-ACLScanner 
Now, to check for modify rights/permissions for the student157 or RDPUsers group, Invoke-ACLScanner can be used
```
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "dcorp\student157"}
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "student157"}
PS C:\AD\Tools> Get-NetGroupMember -GroupName rdpusers --list of rdp users from group
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "rdpusers"}
```
Search for interesting ACEs	
``` powershell
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs  --> interesting ACLS are where can write or modify any interesting once rather then read one.
```
Get the ACLs associated with the specified path	
``` powershell
PS C:\AD\Tools> Get-PathAcl -Path "\\dcorp-dc.dollarcorp.moneycorp.local\sysvol"
```
