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

Trust is a relationship that is established between two domains or forests to enable users in one domain to access resources in the other domain. Trusts allow users to authenticate to a domain controller in one domain and then access resources in another domain without having to re-authenticate.

