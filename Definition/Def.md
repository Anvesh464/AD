# Active Directory

Active Directory (AD) is a **centralized device** used for managing the organization's devices, enabling centralized and secure management of an entire network. It stores information about objects on the network and makes it available to both users and admins. AD is a **directory service** used to manage Windows networks.

---

## Objective

- **AD** ➔ **Windows Server** ➔ Account information, privileges, profiles policy.
- **Windows Servers** ➔ Management profiles, network information, printers, file shares policy.
- **Network Devices** ➔ Configuration, quality of service policy, security policy.

---

## Components of Active Directory

Active Directory consists of several components, each serving a specific role:

### 1. **Domain**
A logical group of objects (e.g., users, computers, printers) sharing a common directory database.

### 2. **Domain Controller**
A server that manages user authentication, authorization, and stores domain object information.

### 3. **Active Directory Schema**
Defines the structure and attributes of directory objects. It's replicated across domain controllers.

### 4. **Forest**
A collection of one or more domains with a common schema, providing a security boundary and trust relationships between domains.

### 5. **Global Catalog**
A distributed data store containing a subset of directory information from all domains, enabling cross-domain object searches.

### 6. **Organizational Unit (OU)**
A container object used to group and organize directory objects, providing a mechanism for delegation and policy application.

### 7. **Trusts**
Relationships between domains (in the same or different forests) that allow resource sharing and authentication.

---

## Group Policy

**Group Policy** is a feature within Active Directory allowing administrators to define and enforce policies for users and computers.

### Key Features:
1. **Centralized Management:** Manage policies for entire domains or specific user/computer groups.
2. **Policy Inheritance:** Settings can be inherited from parent objects in the AD hierarchy.
3. **Filtering & Targeting:** Policy settings can be filtered based on attributes like group membership or IP address.
4. **Security Settings:** Enforce password policies, lockout policies, user rights, etc.
5. **Software Installation:** Deploy and manage software across the domain.
6. **Desktop Configuration:** Configure user desktops (e.g., wallpaper, Start menu).

---

## Access Control Lists (ACLs)

ACLs control access to objects/resources by specifying permissions for users or groups.

- **Access Token:** Security context identifying user privileges.
- **Security Descriptors:** Contains the SID of the owner, DACL, and SACL.
- **ACE:** Specifies individual permissions (allow, deny, or audit).
- **DACL:** Defines permissions for trustees (users or groups).
- **SACL:** Logs audit success or failure when accessing an object.

---

## Trust Relationships

A **Trust** allows users from one domain to access resources in another domain without re-authentication. Trusts can be established between domains or forests.

---

## Trust Types

### 1. **One-Way Trust** 
- **Uni-directional**: Users in the trusted domain can access resources in the trusting domain.  
![One-Way Trust](https://github.com/user-attachments/assets/b4f86e38-b92a-4f90-80c3-10a8b826d283)

### 2. **Two-Way Trust**
- **Bi-directional**: Users in both domains can access resources in each other’s domain.  
![Two-Way Trust](https://github.com/user-attachments/assets/39be0def-7bbc-4fd7-a119-8c8add283d96)

### 3. **Transitive Trust**
- A **Transitive Trust** can extend beyond the two domains where it was created. If Domain A trusts Domain B, and Domain B trusts Domain C, then Domain A also trusts Domain C.

**Example Types:**
- Infra-forest trust
- Tree-root trust
- Parent-child trust  
[More Information](https://blogs.msmvps.com/acefekay/2016/11/02/active-directory-trusts/)

### 4. **Non-Transitive Trust**
- **Non-extendable** to other domains in the forest. Typically, this is an **External Trust**.  
![Non-Transitive Trust](https://github.com/user-attachments/assets/1aefa6f2-ba6c-46f6-8bad-3469536d8b4c)

### 5. **Shortcut Trust**
- Explicitly created transitive trust to **improve authentication speed** between two domains.  
![Shortcut Trust](https://github.com/user-attachments/assets/5f5b052b-9597-4a8d-ab5a-5950a7c6cc28)

### 6. **Parent-Child Trust**
- Automatically created when a **child domain** is added to the **parent domain**. A transitive, two-way relationship.  
![Parent-Child Trust](https://github.com/user-attachments/assets/a470bf58-09b7-4ba0-800f-a0ddd658f382)

### 7. **Tree-Root Trust**
- **Transitive, two-way** trust automatically created between the **forest root domain** and a **new tree**.  
![Tree-Root Trust](https://github.com/user-attachments/assets/5e3565ba-1555-4325-9598-8e9dfdb81775)

### 8. **External Trust**
- A **one-way, non-transitive** trust used to link domains in **different forests**.  
![External Trust](https://github.com/user-attachments/assets/406751e9-cd1f-4359-8d18-b2cfb6dd201c)

### 9. **Forest Trust**
- A **transitive, one-way or two-way** trust between multiple forests. It allows access between different forests.  
![Forest Trust](https://github.com/user-attachments/assets/67f1741b-d9f9-46ca-b14a-3bf95311fd84)

### 10. **Realm Trust**
- Connects Active Directory with **non-Windows systems** (e.g., Unix). **Transitive or non-transitive**.  
![Realm Trust](https://github.com/user-attachments/assets/962a83fc-a698-4264-8070-23ef9add8398)

---

**Conclusion**  
Understanding the components of Active Directory and the different trust relationships helps administrators efficiently manage a network's security, access control, and resources. By leveraging tools like Group Policy and ACLs, AD ensures a centralized and secure environment for managing users, computers, and devices across the network.

---
