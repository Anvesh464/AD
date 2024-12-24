### Understanding Kerberos Delegation in Active Directory

**Kerberos Delegation** is a feature in Microsoft Active Directory (AD) that allows a service to use a user's credentials to access other resources or services on their behalf. This is useful in multi-tier applications where a service (e.g., a web server) needs to access another service (e.g., a database server) on behalf of the user who initially authenticated to the web server.

### Types of Kerberos Delegation

There are two main types of Kerberos delegation in Active Directory:

1. **Unconstrained Delegation (General/Basic Delegation)**:
   - **Definition**: This type of delegation allows a service (e.g., a web server) to impersonate a user and request access to any service on any computer within the domain.
   - **Risk**: It is less secure because the service can potentially impersonate a user and access resources it should not have access to, increasing the attack surface for privilege escalation.

2. **Constrained Delegation**:
   - **Definition**: A more secure type of delegation where administrators can specify which services a service account is allowed to delegate to, and which resources it can access. For example, a web server might be constrained to only request access to a specific database server and only for certain services.
   - **Security Advantage**: Constrained delegation reduces the risk by limiting the scope of the service's delegation, preventing it from impersonating a user and accessing all services across the domain.

### The Importance of Kerberos Delegation in Multi-Tier Applications

Kerberos delegation is crucial in environments where there are **multi-tier applications** (e.g., web applications that need to interact with a backend database). Without delegation, the service (such as a web server) would need to authenticate with its own service account rather than using the credentials of the user who initiated the request. This limits the ability to access resources or perform actions that require the user’s specific permissions.

#### Example:
- A user authenticates to a **web server**.
- The **web server** needs to access a **database server** on behalf of the user.
- With **Kerberos Delegation**, the web server can request access to the database server **as the user** and perform actions on their behalf (e.g., access data, execute queries).

For this to work, the **web server's service account** must be trusted for delegation to be able to impersonate the user and access the necessary resources.

### How Kerberos Delegation Works

- **Delegation Process**: When a user logs in to a service (e.g., the web server), they authenticate to the **domain controller** (DC) and receive a **Kerberos ticket**. If the service is configured for delegation, it can request a **Service Ticket** for other resources (e.g., a database server) on behalf of the user.
- **Double-Hop Scenario**: Kerberos delegation is essential for the **Kerberos double-hop** scenario, where a service must authenticate to another service using the user’s credentials. Without delegation, the service would authenticate to the second service using its own account, not the user's credentials, limiting the ability to perform actions on behalf of the user.

### Security Risks and Attacks

#### 1. **Unconstrained Delegation Attack**:
- **Attack Vector**: If a service account has **Unconstrained Delegation** enabled, an attacker who compromises that service account can request tickets for any user in the domain. They can then use these tickets to impersonate the user and access sensitive resources across the domain.
- **Mitigation**: **Unconstrained delegation** should be avoided. Instead, use **Constrained Delegation** to limit the scope of delegation and specify which services can be delegated.

#### 2. **Constrained Delegation Attack**:
- **Attack Vector**: If an attacker gains access to a service account with **Constrained Delegation** enabled, they may be able to escalate privileges by impersonating a user to gain unauthorized access to specific resources. 
- **Mitigation**: Properly configure constrained delegation by specifying which services the service can delegate to and regularly auditing delegation settings to ensure they are tightly controlled.

### Best Practices for Securing Kerberos Delegation

1. **Limit the use of Unconstrained Delegation**: Unconstrained Delegation should be avoided wherever possible due to the security risks associated with it.
2. **Implement Constrained Delegation**: Constrained Delegation provides a more secure alternative, ensuring services only have permission to access specified resources on specific machines.
3. **Enable Protocol Transition**: If a service needs to accept non-Kerberos authentication (e.g., NTLM), **Protocol Transition** can be used to convert non-Kerberos tickets to Kerberos tickets, thus improving security while allowing delegation.
4. **Monitor for Abnormal Activity**: Continuously monitor service accounts and delegation configurations to detect unusual access patterns or privilege escalation attempts.
5. **Enforce Least Privilege**: Ensure that users and services have the minimum required permissions to perform their tasks. This helps mitigate the impact of potential attacks.

### Conclusion

Kerberos Delegation is a powerful feature in Microsoft Active Directory that allows services to impersonate users to access other resources. However, improper configuration of delegation can lead to security vulnerabilities, including **Unconstrained Delegation attacks** and **Constrained Delegation privilege escalation**. To mitigate these risks, organizations should carefully manage service accounts, enforce least privilege principles, and implement **Constrained Delegation** wherever possible to limit the scope of delegation.

![image](https://github.com/user-attachments/assets/58634ad4-72be-4f15-8ba8-829040b3c595)

![image](https://github.com/user-attachments/assets/5e70971b-ae11-4887-9cb0-625010fb2332)

![image](https://github.com/user-attachments/assets/78fbabfa-cfff-4269-81bb-13a36a5ba067)

# Unconstrained Delegation Attack Overview

## Steps Involved

1. **User Authentication:**
    - A user provides credentials to the Domain Controller (DC).
    - The DC returns a Ticket Granting Ticket (TGT).
2. **Service Ticket Request:**
    - The user requests a Ticket Granting Service (TGS) ticket for the web service on a Web Server.
    - The DC provides a TGS.
3. **TGT and TGS Transmission:**
    - The user sends the TGT and TGS to the web server.
4. **Service Account Delegation:**
    - The web server service account uses the user's TGT to request a TGS for the database server from the DC.
    - The web server service account connects to the database server as the user.
5. **Unconstrained Delegation:**
    - The web server not only authenticates to the database server but also to any server in the domain, demonstrating Unconstrained Delegation.

## Unconstrained Delegation Explained

When set for a particular service account, unconstrained delegation allows delegation to any service to any resource on the domain as a user.

When unconstrained delegation is enabled, the DC places the user's TGT inside the TGS (Step 4 in the previous diagram). When presented to the server with unconstrained delegation, the TGT is extracted from the TGS and stored in LSASS. This way, the server can reuse the user's TGT to access any other resource as the user.

This could be used to escalate privileges in case we can compromise the computer with unconstrained delegation and a Domain Admin (DA) connects to that machine.

## Attack Execution

### Load Mimikatz Script and Export Tickets

Load the Mimikatz script on the remote machine and export the tickets. If there is no interesting ticket, we need to wait for an interesting ticket to be present.

### Waiting for a Domain Admin to Access a Resource

We can use PowerView's `Invoke-UserHunter` to wait or trick a DA to access a resource on `dcorp-adminsrv`.

```powershell
# Load PowerView Dev
PS C:\AD\Tools> . .\PowerView_dev.ps1

# Use Invoke-UserHunter to wait for a particular DA to access a resource
PS C:\AD\Tools> Invoke-UserHunter -UserName <TargetDA> -ComputerName dcorp-adminsrv
```

This approach helps in escalating privileges by exploiting unconstrained delegation, making it easier to capture a DA's credentials and reuse them for accessing other resources within the domain.
