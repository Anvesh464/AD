#Get all the groups in the current domain
 
Get-DomainGroup
Get-DomainGroupMember -Name "Domain Admins"
Get-DomainGroup –Domain <targetdomain>  --> if you are enumerate on another domains
 Get-DomainGroupMember -Name "Domain Admins" -domain "domain name"
#Get all the members of the Domain Admins group
Get-NetGroupMember -GroupName "Domain Admins"
Get-NetGroupMember  -GroupName "Domain Admins" -Recurse
 
Get-DomainOU
Get-NetGroupMember -GroupName "Enterprise Admins" -Domain <DOmain name here>
#Get the group membership for a user:
Get-DomainGroup –UserName "student1"
 
 
 
 
 
Recurve enabled Get-NetGroupMember  -GroupName "Domain Admins" -Recurse - almost most of the time will be     same results 
 
 
 
Get the group membership for a user:
 
 
 

