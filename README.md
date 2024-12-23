Active Directory: 

• Active directory is a centralized device which we used for managing the organization devices and it is centralized and secure management of an entire network.
• It stores information about objects on the network and makes it easily available to users and admins
• Directory service used to managed the windows network.

Objective like 

AD --> windows server --> Account information, privileges, profiles policy.
Windows Servers --> management profiles, network information, printers, file shares policy.
Network Devices: Configuration, quality of service policy, security policy.

Components: Active Directory (AD) is composed of several different components, each of which plays a specific role in the management and operation of the directory service. The main components of AD include:
	
	l Domain: A domain is a logical group of objects, such as users, computers, and printers, that share a common directory database. A domain is identified by a unique name and is managed as a single entity with its own policies and security settings.
	l Domain Controller: A domain controller is a server that manages user authentication and authorization, and stores information about objects in the domain. Each domain has at least one domain controller, which is responsible for managing and replicating directory information to other domain controllers in the same domain.
	l Active Directory Schema: The AD schema defines the structure and attributes of objects in the directory. The schema is stored on each domain controller and is replicated to all other domain controllers in the domain.
	l Forest: A forest is a collection of one or more domains that share a common schema and global Catalog. A forest is a security boundary and provides a mechanism for defining trust relationships between domains.
	l Global Catalog: The global catalog is a distributed data store that contains a subset of directory information from all domains in the forest. It provides a mechanism for searching and locating objects across domains.
	l Organizational Unit (OU): An organizational unit is a container object that can be used to group and organize other objects in the directory, such as users, computers, and groups. OUs provide a mechanism for delegating administrative control and applying policies to a specific subset of objects.
	l Trusts: Trusts are used to define relationships between domains in the same forest or in different forests. Trusts provide a mechanism for sharing resources and enabling users in one domain to access resources in another domain.

Each of these components plays an important role in the management and operation of Active Directory. By understanding these components, administrators can effectively manage and secure their directory services.

Group Policy: 

Group Policy is a feature of Active Directory that enables administrators to define and enforce policies and settings for users and computers in a Windows domain. Group Policy settings are stored in the Group Policy Objects (GPOs), which are applied to Active Directory objects, such as users, computers, and groups.

Group Policy enables administrators to manage a wide range of settings, including security settings, software installation, desktop configuration, and network connections. Some of the key features of Group Policy in Active Directory include:

	1. Centralized management: Group Policy enables administrators to manage policy settings for the entire domain or for specific groups of users or computers from a single location.
	2. Policy inheritance: Group Policy settings can be inherited from parent objects in the Active Directory hierarchy, providing a mechanism for defining default settings and exceptions to those settings.
	3. Filtering and targeting: Group Policy provides a mechanism for filtering and targeting policy settings based on user and computer attributes, such as group membership, organizational unit, and IP address.
	4. Security settings: Group Policy enables administrators to define and enforce security settings, such as password policies, account lockout policies, and user rights.
	5. Software installation: Group Policy can be used to deploy and manage software installations across the domain.
	6. Desktop configuration: Group Policy provides a mechanism for configuring desktop settings, such as the wallpaper, screensaver, and Start menu.

Group Policy is a powerful tool for managing and securing Windows domains, and is widely used in enterprise environments. By defining and enforcing consistent policy settings across the domain, administrators can ensure that their network is secure, compliant, and running efficiently.

ACL's: ACL's is a list of access control entries (ACE), each ACE is an ACL identifies a trustee and specific the access rights allowed, denied audited for that trustee.
Enables control on the ability allowed, denied and audited for that trustee.
Enables control on the ability of process to access objects and other resources.

Access Token (security context of a process identify and privilege of users)

Security descriptions ( SID of the owner, discretionary ACLS - DACL and system ACL - SACL

ACE: ACE correspond to individual permission or audits access. Who has permission and what can be done on an object?

DACL: Defines the permission trustee a user or group have on an object and what kinds  on an object and what kinds of operations they can perform.
SACL: Logs success and failure audit messages when an object is accessed.

Trust: Trust is a relationship that is established between two domains or forests to enable users in one domain to access resources in the other domain. Trusts enable users to authenticate to a domain controller in one domain, and then access resources in another domain without having to re-authenticate.

Trust Direction: 

One way Trust: Uni-directional, users in the trusted domain can be access resource in the trusting domain. 
One-way trusts are only given from one domain to another.

Two- way Trust: Bi-direction users for both domains can access resource in the another domain.
 
Transitive trust: 

A transitive trust is when a trust can be extended outside of the two domains in which it was created. A domain connected via a transitive trust can thus access any other domain when there is a path of transitive trusts between that domain and the target domain.

Transitive trust is a two-way relationship automatically created between parent and child domains in a Microsoft Active Directory forest. When a new domain is created, it shares resources with its parent domain by default, enabling an authenticated user to access resources in both the child and parent. 

The transitive property of equality states that if a = b and b = c, then a = c.  In an Active Directory transitive trust relationship, if domain A trusts domain B, and domain B trusts domain C, then domain A trusts domain C.

Examples: infra-forest trust, tree - root trust, parent-child trust

https://blogs.msmvps.com/acefekay/2016/11/02/active-directory-trusts/

Non-Transitive Trust: Cannot be extended to other domains in the forest. Can be two way or one-way. It is called external trust.

Shortcut Trust:

Shortcut Trust is an explicitly created, transitive trust between two domains in a forest to improve user logon times. Shortcut Trust will make a trust path shorter between two domains in the same forest. The Shortcut Trust can be one-way or two-way.

Parent-Child Trust

A transitive, two-way parent-child trust relationship automatically created and establishes a relationship between a parent domain and a child domain whenever a new child domain is created using the AD DS installation process process within a domain tree. They can only exist between two domains in the same tree with the same contiguous namespace. The parent domain is always trusted by the child domain. You cannot manually create a Parent-Child trust.

Tree-Root Trust:

A transitive, two-way tree-root trust relationship automatically created and establishes a relationship between the forest root domain and a new tree, when you run the AD DS installation process to add a new tree to the forest. A tree-root trust can only be established between the roots of two trees in the same forest and are always transitive. You cannot manually create a tree-root trust.

Shortcut Trust:

Shortcut trusts are manually created, one-way, transitive trusts. They can only exist within a forest. They are created to optimize the authentication process shortening the trust path. The trust path is the series of domain trust relationships that the authentication process must traverse between two domains in a forest that are not directly trusted by each other. Shortcut trusts shorten the trust path.

A shortcut trust is an AD trust relationship that administrators can explicitly define in addition to the trust relationships that AD automatically creates between the domains in an AD forest as part of the AD installation process (dcpromo).

	• Used to reduce access times in complex trust scenario.
	• Can be one-way or two-way transitive.

External trust:  

An external trust is a trust between domains in different forests.  An external trust is a one-way, non-transitive trust that is manually created to establish a trust relationship between AD DS domains that are in different forests, or between an AD DS domain

External trusts allow you to provide users access to resources in a domain outside of the forest that is not already trusted by a Forest trust.

Forest Trust: Forest trusts are manually created, one-way transitive, or two-way transitive trusts that allow you to provide access to resources between multiple forests. Forest trusts uses both Kerberos v5 and NTLM authentication across forests where users can use their Universal Principal Name (UPN) or their Pre-Windows 2000 method (domain Name\username). Kerberos v5 is attempted first, and if that fails, it will then try NTLM.

Realm Trust:

A realm trust is used to connect Active Directory with Kerberos V5 realm on a non-Windows system like Unix. In order to create a realm trust, the domain must be at the Windows Server 2003 functional level or higher. These can be transitive or non-transitive, one-way or two.
