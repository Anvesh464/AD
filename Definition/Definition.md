# Active Directory

- Active Directory is a centralized device used for managing the organization's devices with centralized and secure management of an entire network.
- It stores information about objects on the network and makes it easily available to users and admins.
- Directory service used to manage the Windows network.

## Objective

- **AD** ➔ Windows Server ➔ Account information, privileges, profiles policy.
- **Windows Servers** ➔ Management profiles, network information, printers, file shares policy.
- **Network Devices** ➔ Configuration, quality of service policy, security policy.

## Components

Active Directory (AD) is composed of several different components, each of which plays a specific role in the management and operation of the directory service. The main components of AD include:

- **Domain**: A domain is a logical group of objects, such as users, computers, and printers, that share a common directory database. A domain is identified by a unique name and is managed as a single entity with its own policies and security settings.
- **Domain Controller**: A domain controller is a server that manages user authentication and authorization and stores information about objects in the domain. Each domain has at least one domain controller, which is responsible for managing and replicating directory information to other domain controllers in the same domain.
- **Active Directory Schema**: The AD schema defines the structure and attributes of objects in the directory. The schema is stored on each domain controller and is replicated to all other domain controllers in the domain.
- **Forest**: A forest is a collection of one or more domains that share a common schema and global catalog. A forest is a security boundary and provides a mechanism for defining trust relationships between domains.
- **Global Catalog**: The global catalog is a distributed data store that contains a subset of directory information from all domains in the forest. It provides a mechanism for searching and locating objects across domains.
- **Organizational Unit (OU)**: An organizational unit is a container object that can be used to group and organize other objects in the directory, such as users, computers, and groups. OUs provide a mechanism for delegating administrative control and applying policies to a specific subset of objects.
- **Trusts**: Trusts are used to define relationships between domains in the same forest or in different forests. Trusts provide a mechanism for sharing resources and enabling users in one domain to access resources in another domain.

By understanding these components, administrators can effectively manage and secure their directory services.

## Group Policy

Group Policy is a feature of Active Directory that enables administrators to define and enforce policies and settings for users and computers in a Windows domain. Group Policy settings are stored in the Group Policy Objects (GPOs), which are applied to Active Directory objects, such as users, computers, and groups.

Group Policy enables administrators to manage a wide range of settings, including security settings, software installation, desktop configuration, and network connections. Some of the key features of Group Policy in Active Directory include:

1. **Centralized management**: Group Policy enables administrators to manage policy settings for the entire domain or for specific groups of users or computers from a single location.
2. **Policy inheritance**: Group Policy settings can be inherited from parent objects in the Active Directory hierarchy, providing a mechanism for defining default settings and exceptions to those settings.
3. **Filtering and targeting**: Group Policy provides a mechanism for filtering and targeting policy settings based on user and computer attributes, such as group membership, organizational unit, and IP address.
4. **Security settings**: Group Policy enables administrators to define and enforce security settings, such as password policies, account lockout policies, and user rights.
5. **Software installation**: Group Policy can be used to deploy and manage software installations across the domain.
6. **Desktop configuration**: Group Policy provides a mechanism for configuring desktop settings, such as the wallpaper, screensaver, and Start menu.

By defining and enforcing consistent policy settings across the domain, administrators can ensure that their network is secure, compliant, and running efficiently.

## ACLs

ACLs (Access Control Lists) are a list of access control entries (ACE), each ACE in an ACL identifies a trustee and specifies the access rights allowed, denied, or audited for that trustee. ACLs enable control on the ability of a process to access objects and other resources.

- **Access Token**: The security context of a process identifying and the privilege of users.
- **Security Descriptors**: Includes SID of the owner, Discretionary ACLs (DACL), and System ACL (SACL).
- **ACE**: ACE corresponds to individual permissions or audit access, specifying who has permission and what can be done on an object.
- **DACL**: Defines the permission trustees (users or groups) have on an object and the kinds of operations they can perform.
- **SACL**: Logs success and failure audit messages when an object is accessed.

## Trust

Trust is a relationship that is established between two domains or forests to enable users in one domain to access resources in the other domain. Trusts allow users to authenticate to a domain controller in one domain and then access resources in another domain without having to re-authenticate.<br>

<img src="https://github.com/user-attachments/assets/e21eb86c-6321-4e1e-9b30-969e3f84b4fb" alt="Image 1" width="750"/>

# Trust Direction

## One-Way Trust
- **One-way Trust**: Uni-directional, users in the trusted domain can access resources in the trusting domain. One-way trusts are only given from one domain to another.

![image](https://github.com/user-attachments/assets/b4f86e38-b92a-4f90-80c3-10a8b826d283)

## Two-Way Trust

- **Two-way Trust**: Bi-directional, users in both domains can access resources in the other domain.

![image](https://github.com/user-attachments/assets/39be0def-7bbc-4fd7-a119-8c8add283d96)

## Transitive Trust

- **Transitive Trust**: A transitive trust can be extended outside of the two domains in which it was created. A domain connected via a transitive trust can thus access any other domain when there is a path of transitive trusts between that domain and the target domain.
- Transitive trust is a two-way relationship automatically created between parent and child domains in a Microsoft Active Directory forest. When a new domain is created, it shares resources with its parent domain by default, enabling an authenticated user to access resources in both the child and parent.
- The transitive property of equality states that if a = b and b = c, then a = c. In an Active Directory transitive trust relationship, if domain A trusts domain B, and domain B trusts domain C, then domain A trusts domain C.

Examples: infra-forest trust, tree-root trust, parent-child trust

[More Information](https://blogs.msmvps.com/acefekay/2016/11/02/active-directory-trusts/)

## Non-Transitive Trust

- **Non-Transitive Trust**: Cannot be extended to other domains in the forest. Can be two-way or one-way. It is called external trust.

![image](https://github.com/user-attachments/assets/1aefa6f2-ba6c-46f6-8bad-3469536d8b4c)

## Shortcut Trust

- **Shortcut Trust**: An explicitly created, transitive trust between two domains in a forest to improve user logon times. Shortcut Trust will make a trust path shorter between two domains in the same forest. The Shortcut Trust can be one-way or two-way.

![image](https://github.com/user-attachments/assets/5f5b052b-9597-4a8d-ab5a-5950a7c6cc28)

## Parent-Child Trust

- **Parent-Child Trust**: A transitive, two-way parent-child trust relationship automatically created and establishes a relationship between a parent domain and a child domain whenever a new child domain is created using the AD DS installation process within a domain tree. They can only exist between two domains in the same tree with the same contiguous namespace. The parent domain is always trusted by the child domain. You cannot manually create a Parent-Child trust.

![image](https://github.com/user-attachments/assets/a470bf58-09b7-4ba0-800f-a0ddd658f382)

## Tree-Root Trust

- **Tree-Root Trust**: A transitive, two-way tree-root trust relationship automatically created and establishes a relationship between the forest root domain and a new tree, when you run the AD DS installation process to add a new tree to the forest. A tree-root trust can only be established between the roots of two trees in the same forest and are always transitive. You cannot manually create a tree-root trust.

![image](https://github.com/user-attachments/assets/5e3565ba-1555-4325-9598-8e9dfdb81775)

## Shortcut Trust

- **Shortcut Trust**: Manually created, one-way, transitive trusts. They can only exist within a forest. They are created to optimize the authentication process by shortening the trust path. The trust path is the series of domain trust relationships that the authentication process must traverse between two domains in a forest that are not directly trusted by each other. Shortcut trusts shorten the trust path.

  - Used to reduce access times in complex trust scenarios.
  - Can be one-way or two-way transitive.

![image](https://github.com/user-attachments/assets/5701accc-b559-4de1-a878-51978a9e98b9)

## External Trust

- **External Trust**: A trust between domains in different forests. An external trust is a one-way, non-transitive trust that is manually created to establish a trust relationship between AD DS domains that are in different forests, or between an AD DS domain.
- External trusts allow you to provide users access to resources in a domain outside of the forest that is not already trusted by a forest trust.

![image](https://github.com/user-attachments/assets/406751e9-cd1f-4359-8d18-b2cfb6dd201c)

## Forest Trust

- **Forest Trust**: Manually created, one-way transitive, or two-way transitive trusts that allow you to provide access to resources between multiple forests. Forest trusts use both Kerberos v5 and NTLM authentication across forests where users can use their Universal Principal Name (UPN) or their Pre-Windows 2000 method (domain Name\username). Kerberos v5 is attempted first, and if that fails, it will then try NTLM.

![image](https://github.com/user-attachments/assets/67f1741b-d9f9-46ca-b14a-3bf95311fd84)

## Realm Trust

- **Realm Trust**: Used to connect Active Directory with Kerberos V5 realm on a non-Windows system like Unix. In order to create a realm trust, the domain must be at the Windows Server 2003 functional level or higher. These can be transitive or non-transitive, one-way or two-way.

![image](https://github.com/user-attachments/assets/962a83fc-a698-4264-8070-23ef9add8398)
