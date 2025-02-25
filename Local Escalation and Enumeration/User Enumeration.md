# User and Group Information Commands

This document contains a collection of useful commands for retrieving user and group information in Windows environments.

### Get Current Username

To get the current username:
```
-echo %USERNAME% || whoami
$env:username
```
### List user privilege
```
whoami /priv
whoami /groups
```
### List all users
```
net user
whoami /all
Get-LocalUser | ft Name,Enabled,LastLogon
Get-ChildItem C:\Users -Force | select Name
```
### List logon requirements; useable for bruteforcing
```
net accounts
```
### Get details about a user (i.e. administrator, admin, current user)
```
net user administrator
net user admin
net user %USERNAME%
```
### List all local groups
```
net localgroup
Get-LocalGroup | ft Name
```
### Get details about a group (i.e. administrators)
```
net localgroup administrators
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
Get-LocalGroupMember Administrateurs | ft Name, PrincipalSource
```
