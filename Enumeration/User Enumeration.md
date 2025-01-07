# PowerView Module Usage

# User Enumeration
 ## Basic Enumeration of a user 
 Get a list of users in the current domain
 ```powershell
 PS C:\AD\Tools> Get-NetUser   -- it will get list of all the users and default properties in current domain.
 PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser
 PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Filter * -Properties *
 ```
 All users
 ```powershell
PS C:\AD\Tools>  Get-NetUser | select name 
PS C:\AD\Tools> Get-NetUser | select -ExpandProperty samaccountname
PS C:\AD\Tools> Get-NetUser -Domain moneycorp.local | select cn  --> trusted Domain userlist
PS C:\AD\Tools> Get-NetUser | select cn   --> current Domain user list (common name)
PS C:\AD\Tools> Get-NetUser -Domain moneycorp.local | select cn  --> trusted Domain userlist
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Filter * -Properties * | select name
```
Basic Enemeration on particular user
```powershell
PS C:\AD\Tools> Get-NetUser -UserName student157
PS C:\AD\Tools> Get-NetUser -UserName student157 | select logoncount
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Identity student157
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Identity student157 -Properties *
```
## User properties
Get list of all properties for users in the current domain
```powershell
PS C:\AD\Tools> Get-UserProperty  --> current domain userproperty list
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Filter * -Properties *  | select -First 1 | Get-Member -MemberType *property | select name
```
## Get List of All Properties for Users in the Current Domain
To get various user properties:
```powershell
Get-DomainUser –Properties pwdlastset
Get-DomainUser –Properties badpwdcount
Get-DomainUser –Properties lastlogon
Get-DomainUser –Properties description
Get-DomainUser -Properties samaccountname,description
Get-DomainUser -Properties samaccountname,description,mumberof
```
```powershell
PS C:\AD\Tools> Get-UserProperty -Properties pwdlastset
PS C:\AD\Tools> Get-UserProperty -Properties badpasswordtime
PS C:\AD\Tools> Get-UserProperty -Properties logoncount
PS C:\AD\Tools> Get-UserProperty -Properties description
```

Search for a particular string in a user's attributes: in Discription level
```powershell
PS C:\AD\Tools> Find-UserField -SearchField Description -SearchTerm "built" or password etc...
PS C:\AD\Tools\ADModule-master\ADModule-master> Get-ADUser -Filter 'Discription -like "*built*"' -Properties Description  | select Name,Discription
PS C:\AD\Tools\ADModule-master\ADModule-master>
```
## Find User Accounts Used as Service Accounts
```powershell
Get-DomainUser -SPN
```

## Filter Users by Account Status
To get all enabled users (returning distinguished names):
```powershell
Get-DomainUser -UACFilter NOT_ACCOUNTDISABLE -Properties distinguishedname
```

To get all disabled users:
```powershell
Get-DomainUser -UACFilter ACCOUNTDISABLE
```

## Quick Win
To get the description property for all users:
```powershell
Get-DomainUser –Properties description
```

# Download PowerView
You can download the PowerView script from the following link:
[PowerView Script - GitHub](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)

# Logged on Users on machine (Admin Priv) Sessions
### Get a list of actively logged on users in a computer | Get users that are logged on to a given computer:
```powershell
PS C:\AD\Tools> Get-NetLoggedon  --> local machine
PS C:\AD\Tools> Get-NetLoggedon -ComputerName dcorp-dc.dollarcorp.moneycorp.local  -->  given host for actively logged on users.
First find local admin access after use computer name who loggged on system so we can dump hashes for that machine.
```
### Get locally logged users on a computer (needs remote registry on the target started by default on server OS)	
```powershell
PS C:\AD\Tools>  Get-LoggedonLocal -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```
### Get the last logged user on a computer (needs administrative rights and remote registry on the target)	
```powershell
PS C:\AD\Tools> Get-LastLoggedOn -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```
