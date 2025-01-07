# Active Directory (AD)

Active Directory is a centralized service used to manage the organization's devices, offering secure management of the entire network. It stores information about network objects, making it easily accessible to users and administrators. It is a directory service used to manage the Windows network.

### Objectives
- **AD → Windows Server →** Account information, privileges, profiles policy.
- **Windows Servers →** Management profiles, network information, printers, file shares policy.
- **Network Devices →** Configuration, quality of service policy, security policy.

---

## Key Components of Active Directory (AD)

Active Directory consists of various components that manage and operate the directory service effectively.

| **Component**            | **Description**                                                                                                                                                       |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Domain**               | A domain is a logical group of objects such as users, computers, and printers that share a common directory database. A domain is identified by a unique name and managed as a single entity with its own policies and security settings. |
| **Domain Controller**    | A domain controller is a server responsible for managing user authentication, authorization, and storing information about objects in the domain. Each domain has at least one domain controller that replicates directory information across other domain controllers in the domain. |
| **Active Directory Schema** | Defines the structure and attributes of objects in the directory. The schema is stored on each domain controller and replicated across all domain controllers in the domain. |
| **Forest**               | A forest is a collection of one or more domains that share a common schema and global catalog. It serves as a security boundary and defines trust relationships between domains. |
| **Global Catalog**       | A distributed data store containing a subset of directory information from all domains in the forest. Used for searching and locating objects across domains. |
| **Organizational Unit (OU)** | A container object used to group and organize objects in the directory such as users, computers, and groups. OUs allow delegating administrative control and applying policies to specific subsets of objects. |
| **Trusts**               | Trusts define relationships between domains within the same or different forests. Trusts allow users in one domain to access resources in another domain. |

---

## Group Policy

Group Policy is a feature of Active Directory that allows administrators to define and enforce policies and settings for users and computers in a Windows domain.

### Key Features of Group Policy:

| **Feature**              | **Description**                                                                                                                                                                      |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Centralized management**| Group Policy enables administrators to manage policy settings for the entire domain or for specific groups of users or computers from a single location.                               |
| **Policy inheritance**    | Group Policy settings can be inherited from parent objects in the Active Directory hierarchy, providing a mechanism for defining default settings and exceptions to those settings. |
| **Filtering and targeting**| Group Policy provides a mechanism for filtering and targeting policy settings based on user and computer attributes, such as group membership, organizational unit, and IP address.  |
| **Security settings**     | Group Policy enables administrators to define and enforce security settings, such as password policies, account lockout policies, and user rights.                                   |
| **Software installation** | Group Policy can be used to deploy and manage software installations across the domain.                                                                                                |
| **Desktop configuration** | Group Policy provides a mechanism for configuring desktop settings, such as the wallpaper, screensaver, and Start menu.                                                               |

---

## ACLs (Access Control Lists)

An **ACL** is a list of access control entries (ACE). Each ACE identifies a trustee and specifies the access rights allowed, denied, or audited for that trustee.

### Key Concepts in ACL:
- **Access Token:** Defines the security context of a process, including the identity and privileges of users.
- **Security Descriptors:** Include the SID of the owner and ACLs (DACL and SACL).

#### Types of ACEs:
- **DACL (Discretionary ACL):** Defines permissions a user or group has on an object and what operations they can perform.
- **SACL (System ACL):** Logs audit success and failure messages when an object is accessed.

---

## Trust Types

Trust is a relationship that is established between two domains or forests to enable users in one domain to access resources in the other domain. Trusts enable users to authenticate to a domain controller in one domain, and then access resources in another domain without having to re-authenticate.

![image](https://github.com/user-attachments/assets/d1557048-6a1b-43e8-8d1d-f55f2da324b2)

### 1. **One-way Trust**
- A **one-way** trust is unidirectional: users in the trusted domain can access resources in the trusting domain. It is only given from one domain to another.

![image](https://github.com/user-attachments/assets/7ec89da3-dd78-4ae9-87f8-4dad34c322f1)

### 2. **Two-way Trust**
- A **two-way** trust allows users from both domains to access resources in the other domain.

![image](https://github.com/user-attachments/assets/0f03b0e7-7452-45a4-9a79-8fc7486f1c65)


### 3. **Transitive Trust**
- A **transitive trust** can be extended beyond the two domains where it was created. If Domain A trusts Domain B, and Domain B trusts Domain C, then Domain A trusts Domain C.
- **Examples:** Infra-forest trust, tree-root trust, parent-child trust.

### 4. **Non-Transitive Trust**
- A **non-transitive trust** cannot be extended to other domains in the forest. It can be one-way or two-way.
- Known as **external trust** when established between different forests.

![image](https://github.com/user-attachments/assets/a39d3154-1e8a-4d8c-8094-6a02da5b524d)

### 5. **Shortcut Trust**
- A **shortcut trust** is a transitive trust explicitly created between two domains to optimize logon times by shortening the trust path.
- Can be one-way or two-way.

![image](https://github.com/user-attachments/assets/590afad3-2564-4761-a9a9-d559de9dbd63)

---

## Parent-Child Trust
- **Parent-Child Trust** is automatically created between a parent and a child domain. These trusts are transitive and two-way, and cannot be manually created.

![image](https://github.com/user-attachments/assets/5d278e5e-8012-453c-bea8-399dfd963030)

## Tree-Root Trust
- **Tree-Root Trust** is created automatically between the forest root domain and a new tree when a new tree is added to the forest. It is always transitive.

![image](https://github.com/user-attachments/assets/cb35e78f-80d0-4f25-bc27-733f6fab07d0)

---

## External Trust
- **External Trust** is a one-way, non-transitive trust manually created to establish a relationship between domains in different forests, providing access to resources in the non-trusted forest.

![image](https://github.com/user-attachments/assets/67e1166d-2d8e-4cad-8dc8-51a1e922ab77)

## Forest Trust
- **Forest Trusts** are transitive, one-way, or two-way trusts created between multiple forests. They allow access to resources between forests using Kerberos v5 and NTLM authentication.
- 
![image](https://github.com/user-attachments/assets/9d13d89d-b2a4-4fc3-908f-8c161af16c18)


## Realm Trust
- **Realm Trust** connects Active Directory to a Kerberos V5 realm on non-Windows systems (e.g., Unix). It can be transitive or non-transitive, one-way or two-way.

![image](https://github.com/user-attachments/assets/bd612140-ffb1-4f86-9b86-40763b858b5e)

