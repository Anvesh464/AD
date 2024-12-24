# BloodHound Overview

## What is BloodHound?
BloodHound is an open-source, JavaScript-based single-page application that provides a graphical user interface (GUI) for analyzing and understanding Active Directory trust relationships. It is useful for both **red teams** and **blue teams** in Active Directory environments.

### Purpose:
- **Attackers**: Can use BloodHound to easily identify complex attack paths, such as paths leading to **Domain Admins**, which would otherwise be difficult to find.
- **Defenders**: Can use it to identify and eliminate these attack paths.
- **Both Red and Blue Teams**: Can use it to gain a deeper understanding of privilege relationships in an Active Directory environment.

### Technologies Used:
- **Neo4j Database**
- **Electron**
- **PowerShell Ingestor**

---

## Advantages of Using BloodHound:
- Provides a comprehensive view of the Active Directory environment in a short time.
- Uncovers complex paths that can be exploited to compromise Domain Admins.
- Helps map relationships between objects and identify paths to privileged access.
- Identifies the following:
  - Local Administrator accounts.
  - ACL (Access Control Lists).
  - Unconstrained Delegation.
  - Shortest path to Domain Admins.

---

## Assessment Outcome:
- Misconfigured ACLs and Shares.
- List computers where domain users are local admins.
- Shortest paths to Domain Admins and Active Sessions.
- Find principals with **DCSync** rights.
- Shortest paths to systems with **Unconstrained Delegation**.
- Shortest paths to High-Value Targets.
- Shortest paths from **Kerberoastable** users.
- Find **Kerberoastable** users with the most privileges.

---

## BloodHound Installation Requirements:
- **Java Runtime Environment**.
- **Neo4j Software**.
- **Local Administrator rights**.
- **BloodHound Software**.
- **No SMB signing** on the target computer.
- **No firewall blocking SMB** from the source computer to the attacker.
- **No firewall blocking SMB** from the attacker to the target computer.

---

## Installation Setup:
1. **Download Neo4j** from the [Neo4j website](https://neo4j.com/).
2. **Download BloodHound Software** from GitHub.
3. **Download BloodHound script** from [GitHub](https://github.com/BloodHoundAD/BloodHound).

---

## Script Execution Commands:

1. **Load PowerShell script**:
    ```powershell
    PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> . .\SharpHound.ps1
    ```

2. **Invoke BloodHound collection**:
    ```powershell
    PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> Invoke-BloodHound -CollectionMethod All -Verbose -Domain xyz.net
    ```

3. **Invoke BloodHound for session collection**:
    ```powershell
    PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> Invoke-BloodHound -CollectionMethod Loggedon -Verbose
    ```

### Collection Methods:
- **Default**: Collects local admins, group memberships, domain trusts, and sessions.
- **Group**: Collects only group memberships.
- **LocalGroup**: Collects local admins only.
- **GPOLocalGroup**: Collects local admins using Group Policy Objects.
- **ComputerOnly**: Collects local admins and sessions.
- **Session**: Collects user sessions on machines.
- **LoggedOn**: Collects privileged sessions (requires local admin rights).
- **Trusts**: Enumerates domain trusts for a target domain.
- **ACL**: Collects Access Control Lists.
- **Container**: Collects Containers.
- **All**: Collects all data types listed above.

---

## Useful Links:
- **BloodHound 4.0 Azure Update**: [malwaredevil.com](https://malwaredevil.com/2020/11/20/introducing-bloodhound-4-0-the-azure-update/)
- **BloodHound Documentation**: [BloodHound Docs](https://bloodhound.readthedocs.io/_/downloads/en/latest/pdf/)
- **BloodHound Custom Queries**: [awsmhacks/awsmBloodhoundCustomQueries](https://github.com/awsmhacks/awsmBloodhoundCustomQueries)
- **Attacking AD Series**: [awsmhacks/AttackingAD](https://github.com/awsmhacks/AttackingAD)
- **BloodHound Cypher Queries Cheat Sheet**: [Cypher Cheatsheet](https://hausec.com/2019/09/09/bloodhound-cypher-cheatsheet/)

---

## Example Cypher Queries:
- Find computers where **Domain Admins** are logged in:
    ```cypher
    MATCH (u:User)-[:MemberOf]->(g:Group {name:'DOMAIN ADMINS@GUWW.NET'}) return u.name, u.displayname
    ```

- Find groups that have permissions to a computer:
    ```cypher
    MATCH p=(m:Group)-[r:Owns|WriteDacl|GenericAll|WriteOwner|ExecuteDCOM|GenericWrite|AllowedToDelegate|ForceChangePassword]->(n:Computer)
    WHERE m.name STARTS WITH "DOMAIN USERS" RETURN p
    ```

- Find members of a specific group with extended rights:
    ```cypher
    MATCH p=(m:Group)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChanges|GetChangesAll|HasSession|Owns|ReadLAPSPassword|SQLAdmin|TrustedBy|WriteDACL|WriteOwner|AddAllowedToAct|AllowedToAct]->(t)
    RETURN m.name, TYPE(r), t.name, t.enabled
    ```

---

## Key Visuals in BloodHound:
- **Blue dots** represent users.
- **Yellow dots** represent machines and groups.

---

## Additional Resources:
- [BloodHound Walkthrough - PenTest Partners](https://www.pentestpartners.com/security-blog/bloodhound-walkthrough-a-tool-for-many-tradecrafts/)
- [GitHub - Compass Security BloodHound Queries](https://github.com/CompassSecurity/BloodHoundQueries)
- [BloodHound Cypher Query Primer - Whiteoak Security](https://www.whiteoaksecurity.com/blog/cypher-query-primer-bloodhound/)

### Key Points:
1. **BloodHound Overview**: It gives you a brief about BloodHound's purpose and usage for Active Directory security assessments.
2. **Advantages**: It lists the key benefits BloodHound offers for both attackers and defenders.
3. **Installation**: A step-by-step guide on installing Neo4j, BloodHound software, and running PowerShell scripts to ingest data.
4. **Cypher Queries**: A sample of useful queries to explore the relationships and potential vulnerabilities in Active Directory using BloodHound.
5. **Visuals**: Understanding the icons used in the BloodHound GUI, such as **blue dots** for users and **yellow dots** for machines and groups.

This markdown content is ready to be used in a GitHub README or similar documentation for sharing knowledge about **BloodHound** and how to use it effectively.

**• Setup BloodHound and identify a machine where studentx has local administrative access.** 
```
Start an application: 
Install(not for local installtion) and start the neo4j service as follows:
PS C:\AD\Tools\neo4j-community-3.5.1-windows\neo4j-community-3.5.1\bin> .\neo4j.bat install-service
Neo4j service installed
PS C:\AD\Tools\neo4j-community-3.5.1-windows\neo4j-community-3.5.1\bin> .\neo4j.bat start
Once the service gets started browse to http://localhost:7474
Enter the username: neo4j and password: neo4j. You need to enter a new password. Let's use BloodHound as the new password.
Now, open BloodHound from C:\AD\Tools\BloodHound-win32-x64\BloodHound-win32-x64\BloodHound.exe and provide the following details:
bolt://localhost:7687
Username: neo4j
Password: BloodHound
```
Run the following PowerShell commands to tun BloodHound ingestores to gather data and information about the current domain
```
PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> . .\SharpHound.ps1  --> load powershell script after run function
PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> Invoke-BloodHound -CollectionMethod All -Verbose
Initializing BloodHound at 9:23 AM on 7/1/2020
Waiting for writer thread to finish
Compressing data to C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors\20200701092346_BloodHound.zip.  --> file created 
```
You can upload this file directly to the UI.
some time BloodHound miss session. so check for session are there or not
```
PS C:\AD\Tools\BloodHound-master\BloodHound-master\Ingestors> Invoke-BloodHound -CollectionMethod Loggedon -Verbose  --> for session
Invoke-BloodHound -CollectionMethod All -ExcludeDC
```
```
Invoke-BloodHound -CollectionMethod -Session
Initializing BloodHound at 10:38 AM on 8/3/2020
Resolved Collection Methods to Group, LocalAdmin, Session, Trusts, ACL, Container, RDP, ObjectProps, DCOM
```
