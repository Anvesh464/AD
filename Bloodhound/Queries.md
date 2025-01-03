The queries you provided appear to be written in **Cypher**, the query language for **Neo4j**, a graph database, with a strong focus on Active Directory environments and their associated security analysis.

### Breakdown of the Queries:

1. **Domain Users as Admin on Computers:**
   ```cypher
   MATCH p=(m:Group)-[:AdminTo]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
   ```
   - This query finds the relationship between **DOMAIN USERS** and any computers where they have administrative privileges (`AdminTo` relationship).

2. **Shortest Path from Domain Users to Domain Admins:**
   ```cypher
   MATCH p=ShortestPath((n {name:"DOMAIN USERS@TESTLAB.LOCAL"}) -[*1..]->(m {name: "DOMAIN ADMINS@TESTLAB.LOCAL"})) RETURN p
   ```
   - This query returns the shortest path between **DOMAIN USERS** and **DOMAIN ADMINS**.

3. **Shortest Path from Domain Users to High Value Targets:**
   ```cypher
   MATCH (g:Group),(n {highvalue:true}),p=shortestPath((g)-[*1..]->(n)) WHERE g.name STARTS WITH 'DOMAIN USERS' RETURN p
   ```
   - This finds the shortest path between **DOMAIN USERS** and any nodes marked as `highvalue`.

4. **Finding All Paths from Domain Users to High Value Targets:**
   ```cypher
   MATCH (g:Group) WHERE g.name STARTS WITH 'DOMAIN USERS' MATCH (n {highvalue:true}),p=shortestPath((g)-[r*1..]->(n)) return p
   ```
   - This is similar to the previous query but aims to find all paths, not just the shortest one.

5. **Finding Unwanted Rights for Domain Users:**
   ```cypher
   MATCH p=(m:Group)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
   ```
   - This finds all **DOMAIN USERS** who may have any unexpected or excessive rights (`Owns`, `WriteDacl`, `GenericAll`, etc.) on computers.

6. **Kerberoastable Accounts:**
   ```cypher
   MATCH (n:User) WHERE n.hasspn=true RETURN n
   ```
   - This identifies users with Service Principal Names (SPNs) enabled, which are vulnerable to Kerberoasting attacks.

7. **Kerberoastable Accounts in High Value Groups:**
   ```cypher
   MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
   ```
   - This finds users who have SPNs enabled and are also members of high-value groups.

8. **Workstations Domain Users Can RDP To:**
   ```cypher
   MATCH p=(g:Group)-[:CanRDP]->(c:Computer) WHERE g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server' return p
   ```
   - This finds workstations (non-server machines) that **DOMAIN USERS** can RDP into.

9. **Servers Domain Users Can RDP To:**
   ```cypher
   MATCH p=(g:Group)-[:CanRDP]->(c:Computer) WHERE g.name STARTS WITH 'DOMAIN USERS' AND c.operatingsystem CONTAINS 'Server' return p
   ```
   - Similar to the previous query, but focuses on servers where **DOMAIN USERS** have RDP access.

10. **Computers with the Most Admins:**
    ```cypher
    MATCH (n:User),(m:Computer), (n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m, count(r) as rel_count order by rel_count desc LIMIT 10 MATCH p=(m)<-[r:AdminTo]-(n) RETURN p
    ```
    - This returns the top 10 computers with the most administrators assigned to them.

11. **Counting High Value Targets a Node Can Reach:**
    ```cypher
    MATCH p = shortestPath((n)-[*1..]->(m {highvalue:true})) WHERE NOT n = m RETURN DISTINCT(m.name),LABELS(m)[0],COUNT(DISTINCT(n)) ORDER BY COUNT(DISTINCT(n)) DESC
    ```
    - This counts how many high-value targets a node can reach (via paths) in the graph.

12. **All DA (Domain Admin) Account Sessions:**
    ```cypher
    MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) MATCH p = (c:Computer)-[:HasSession]->(n) return p
    ```
    - This retrieves all active sessions of **DOMAIN ADMINS** users.

13. **DA Sessions to Non-DC Machines (Servers Excluded):**
    ```cypher
    MATCH (dc:Computer)-[:MemberOf*1..]->(g:Group) WHERE g.name CONTAINS "DOMAIN CONTROLLERS" WITH COLLECT(dc.name) as dcs MATCH (c:Computer) WHERE NOT c.name in dcs MATCH p=(c)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group)
    WHERE g.name STARTS WITH "DOMAIN ADMINS"
    RETURN n.name as Username
    ```
    - This query finds **DOMAIN ADMINS** sessions to non-domain controller computers.

14. **Exclude Paths Involving "AdminTo":**
    ```cypher
    MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}), p=shortestPath((n)-[r*1..]->(m)) WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") RETURN p
    ```
    - This finds paths from a user to **DOMAIN ADMINS** that do not involve `AdminTo` relationships.

15. **Add DOMAIN USERS as Admin to a Specific Computer:**
    ```cypher
    MERGE (n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}) WITH n MERGE (m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) WITH n,m MERGE (n)-[:AdminTo]->(m)
    ```
    - This adds **DOMAIN USERS** as administrators to the computer `COMP00673`.

16. **Delete a Relationship (AdminTo):**
    ```cypher
    MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) DELETE r
    ```
    - This deletes the `AdminTo` relationship between **DOMAIN USERS** and a specific computer.

17. **Set SPN for a User:**
    ```cypher
    MATCH (n:User {name:"JNOA00093@TESTLAB.LOCAL"}) SET n.hasspn=true
    ```
    - This query sets the `hasspn` property to true for the user `JNOA00093`.

### General Observations:

- **High-value Targets**: Many queries are aimed at finding paths to **high-value targets**, such as Domain Admins or machines with critical security settings.
- **RDP Access**: Multiple queries focus on identifying machines that **Domain Users** can access via RDP, including distinguishing between workstations and servers.
- **Kerberoasting**: Several queries look for **Kerberoastable accounts**, i.e., users with SPNs that could potentially be exploited in a Kerberoasting attack.
- **Permissions**: There are queries designed to identify excessive permissions granted to Domain Users, which is a critical area of concern in security assessments.

### Recommendations for Improving or Expanding:

- **Query Performance**: Some queries might become slow with larger datasets (e.g., **Find High Value Users Without Protected User Group**). Optimizing the graph structure and indexing could improve performance.
- **Additional Security Checks**: You may want to expand your queries to check for **lateral movement opportunities**, such as identifying accounts with excessive group memberships or administrative access to sensitive groups.
- **Regular Auditing**: Consider creating scheduled tasks to monitor changes to critical relationships, such as `AdminTo`, `CanRDP`, or changes in group memberships.

This set of queries is useful for security audits, Active Directory management, and identifying potential attack vectors within a network environment. Let me know if you'd like help refining specific queries or optimizing them further!

From <https://raw.githubusercontent.com/Scoubi/BloodhoundAD-Queries/master/BH%20Red2Blue.txt> 

Here are the queries from the provided `BH Red2Blue.txt` file with explanations:

### 1. **Get a count of computers that do not have admins**
```cypher
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name IN compsWithAdmins
RETURN COUNT(c2)
```
- **Explanation**: This query first identifies all computers that have admins assigned to them, and then counts the computers that do not have admins.

---

### 2. **Get the names of computers without admins, sorted in alphabetical order**
```cypher
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name IN compsWithAdmins
RETURN c2.name
ORDER BY c2.name ASC
```
- **Explanation**: Similar to the first query, but instead of counting, it returns the names of computers that do not have admins, ordered alphabetically.

---

### 3. **Return a list of users who have admin rights on at least one system either explicitly or through group membership**
```cypher
MATCH (u:User)-[r:AdminTo|MemberOf*1..]->(c:Computer)
RETURN u.name
```
- **Explanation**: This query returns a list of users who either have direct admin rights on a computer or admin rights through group membership.

---

### 4. **Return username and number of computers that username is admin for, for top N users**
```cypher
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name AS n, COUNT(DISTINCT(C)) AS c 
RETURN n, c
ORDER BY c DESC LIMIT 5
```
- **Explanation**: This query returns the usernames and the number of computers they have admin rights for, limited to the top 5 users with the most admin rights.

---

### 5. **Show all users that are administrator on more than one machine**
```cypher
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name AS n, COUNT(DISTINCT(C)) AS c 
WHERE c > 1
RETURN n
ORDER BY c DESC
```
- **Explanation**: This query shows users who are administrators on more than one computer.

---

### 6. **Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on**
```cypher
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH u, COLLECT(c1) + COLLECT(c2) AS tempVar
UNWIND tempVar AS computers
RETURN u.name AS UserName, COUNT(DISTINCT(computers)) AS AdminRightCount
ORDER BY AdminRightCount DESC
```
- **Explanation**: This query returns users who have administrative rights on at least one machine, ordered by the number of machines they have admin rights on.

---

### 7. **This will return cross-domain 'HasSession' relationships**
```cypher
MATCH p = ((S:Computer)-[r:HasSession*1]->(T:User)) 
WHERE NOT S.domain = T.domain
RETURN p
```
- **Explanation**: This query returns relationships where a user (T) is logged into a computer (S) from a different domain.

---

### 8. **Find all other Rights Domain Users shouldn't have**
```cypher
MATCH p = (m:Group) -> [r:Owns |:WriteDacl |:GenericAll |:WriteOwner |:ExecuteDCOM |:GenericWrite |:AllowedToDelegate |:ForceChangePassword] -> (n:Computer)
WHERE m.name STARTS WITH 'DOMAIN USERS'
RETURN p
```
- **Explanation**: This query identifies inappropriate or excessive rights for the "DOMAIN USERS" group on computers, such as owning or having write permissions.

---

### 9. **Show Kerberoastable high-value targets**
```cypher
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue = true AND n.hasspn = true
RETURN n, g, r
```
- **Explanation**: This query finds users who are members of high-value groups and have Service Principal Names (SPNs) that could be used for Kerberoasting.

---

### 10. **Show computers where Domain Admins are logged in**
```cypher
MATCH (n:User)-[:MemberOf]->(g:Group {name: "DOMAIN ADMINS@EXAMPLE.COM"})
WITH n AS DAaccount
MATCH (c:Computer)-[b:MemberOf]->(t:Group) WHERE NOT t.name = "DOMAIN CONTROLLERS@EXAMPLE.COM"
WITH c AS NonDC
MATCH p = (NonDC)-[:HasSession]->(DAaccount)
```
- **Explanation**: This query identifies computers where members of the "DOMAIN ADMINS" group are logged in, excluding domain controllers.

---

### 11. **Show groups with most local admins**
```cypher
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g, COUNT(c) AS expAdmin
OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c:Computer)
WITH g, expAdmin, COUNT(DISTINCT(c)) AS unrolledAdmin
RETURN g.name, expAdmin, unrolledAdmin, expAdmin + unrolledAdmin AS totalAdmin
ORDER BY totalAdmin DESC
```
- **Explanation**: This query returns groups with the most local admins, including both direct and indirect admin rights through group memberships.

---

### 12. **List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"**
```cypher
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name), u.enabled
ORDER BY u.name
```
- **Explanation**: This query lists unique users who have a path to a "highvalue" group, excluding paths involving the `GetChanges` privilege.

---

### 13. **Return all users which can RDP to any system, if they belong to admin or svr accounts**
```cypher
MATCH (c:Computer) WHERE c.name CONTAINS 'xxxxxx'
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.name = 'DOMAIN ADMINS@EXAMPLE.COM'
OPTIONAL MATCH (g:Group)-[:CanRDP]->(c)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c) WHERE u1.enabled = true AND (u1.name CONTAINS 'ADM' OR u1.name CONTAINS 'SVR')
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c) WHERE u2.enabled = true AND (u2.name CONTAINS 'ADM' OR u2.name CONTAINS 'SVR')
WITH COLLECT(u1) + COLLECT(u2) + COLLECT(n) AS tempVar, c
UNWIND tempVar AS users
RETURN c.name, COLLECT(users.name) AS usernames
ORDER BY usernames DESC
```
- **Explanation**: This query returns users who can RDP to any system, based on being members of "DOMAIN ADMINS" or groups with "ADM" or "SVR" in their name.
Here are the explanations and Cypher queries for the new set of requests:

---

### 14. **Show the number of users that have admin rights on each computer, in descending order**
```cypher
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) AS tempVar, c
UNWIND tempVar AS admins
RETURN c.name AS computerName, COUNT(DISTINCT(admins)) AS adminCount
ORDER BY adminCount DESC
```
- **Explanation**: This query shows the number of users who have admin rights on each computer, counting both direct and indirect admin rights through group memberships, and returns them in descending order of admin count.

---

### 15. **Stats percentage of enabled users that have a path to a high value group**
```cypher
MATCH (u:User {domain:'EXAMPLE.COM',enabled:True})
MATCH (g:Group {domain:'EXAMPLE.COM'})
WHERE g.highvalue = True
WITH g, COUNT(u) as userCount
MATCH p = shortestPath((u:User {domain:'EXAMPLE.COM',enabled:True})-[*1..]->(g))
RETURN toint(100.0 * COUNT(distinct u) / userCount)
```
- **Explanation**: This query calculates the percentage of enabled users who have a path (direct or indirect) to a high-value group, excluding disabled users.

---

### 16. **Shows machines that allow delegation that aren't DCs**
```cypher
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group)  
WHERE NOT t.name =~ "(?i)DOMAIN CONTROLLERS*."  
WITH c AS NonDC  
MATCH (NonDC {unconstraineddelegation:true}) 
RETURN NonDC.name
```
- **Explanation**: This query identifies computers that have unconstrained delegation enabled and ensures they are not domain controllers.

---

### 17. **Groups with most local admins**
```cypher
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g, COUNT(c) AS expAdmin
RETURN g.name, expAdmin, expAdmin AS totalAdmin
ORDER BY totalAdmin DESC
```
- **Explanation**: This query identifies groups with the most local admin rights, counting the number of computers where the group has administrative rights.

---

### 18. **Find principles with DCSync rights**
```cypher
MATCH p = (n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: {result}})
WITH p, n1 
MATCH p2 = (n1)-[:MemberOf|GetChangesAll*1..]->(u:Domain {name: {result}})
WITH p, p2 
MATCH p3 = (n2)-[:MemberOf|GenericAll|AllExtendedRights*1..]->(u:Domain {name: {result}})
RETURN p, p2, p3
```
- **Explanation**: This query looks for users or groups with rights that enable them to perform DCSync operations in a domain, which can be used to extract domain controller password hashes.

---

### 19. **Find all Windows 7 computers**
```cypher
MATCH (c:Computer)
WHERE toUpper(c.operatingsystem) CONTAINS "Windows 7"
RETURN c
```
- **Explanation**: This query returns all computers with "Windows 7" in the `operatingsystem` field.

---

### 20. **Find every user object where the "userpassword" attribute is populated. Neo4j web console only.**
```cypher
MATCH (u:User)
WHERE NOT u.userpassword IS null
RETURN u.name, u.userpassword
```
- **Explanation**: This query returns users whose `userpassword` attribute is populated (i.e., non-null). This can be useful for finding accounts with potentially weak or unencrypted passwords.

---

### 21. **Find every user that doesn't require Kerberos pre-authentication. Neo4j web console only.**
```cypher
MATCH (u:User {dontreqpreauth: true})
RETURN u.name
```
- **Explanation**: This query identifies users that do not require Kerberos pre-authentication, which can be a security risk.

---

### 22. **Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". Neo4j web console only.**
```cypher
MATCH (g:Group)
WHERE g.name =~ '(?i).*SQL.*ADMIN.*'
RETURN g.name
```
- **Explanation**: This query returns groups whose names contain the string "SQL" followed by "ADMIN", such as "SQL ADMINS" or "SQL 2017 ADMINS".

---

### 23. **Find every OU that contains the string "CITRIX".**
```cypher
MATCH (o:OU)
WHERE o.name =~ "(?i).*CITRIX.*"
RETURN o
```
- **Explanation**: This query returns organizational units (OUs) that contain the string "CITRIX" in their names, regardless of case.

---

### 24. **Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.**
```cypher
MATCH (c:Computer)-[:HasSession]->(u:User {domain:'FOO.BAR.COM'})
WHERE NOT c.domain = u.domain
RETURN u.name, COUNT(c)
```
- **Explanation**: This query finds computers that have user sessions from a different domain, which may indicate potential cross-domain compromise opportunities.

---

### 25. **Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.**
```cypher
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer)
RETURN u.name, COUNT(c)
ORDER BY COUNT(c) DESC
```
- **Explanation**: This query identifies users who are trusted to perform constrained delegation and returns them ordered by the number of computers they can delegate to.

---

### 26. **Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.**
```cypher
MATCH (o:OU)-[:Contains]->(c:Computer)
RETURN o.name, o.guid, COUNT(c)
ORDER BY COUNT(c) DESC
```
- **Explanation**: This query returns each organizational unit (OU) in the database, along with the count of computers it contains, sorted by the number of computers.

---

### 27. **Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.**
```cypher
MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
RETURN c.name, c.serviceprincipalnames
ORDER BY c.name ASC
```
- **Explanation**: This query finds computers that have a Service Principal Name (SPN) containing the string "MSSQL", which typically relates to SQL Server instances.

---

### 28. **Find groups with both users and computers that belong to the group. Neo4j web console only.**
```cypher
MATCH (c:Computer)-[r:MemberOf*1..]->(groupsWithComps:Group)
WITH groupsWithComps
MATCH (u:User)-[r:MemberOf*1..]->(groupsWithComps)
RETURN DISTINCT(groupsWithComps) AS groupsWithCompsAndUsers
```
- **Explanation**: This query finds groups that have both computers and users as members.

---

### 29. **Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system of the computer. Neo4j web console only.**
```cypher
MATCH (o:OU)-[:Contains]->(c:Computer)
WHERE toUpper(c.operatingsystem) STARTS WITH "WINDOWS SERVER"
RETURN o.name, c.name, c.operatingsystem
```
- **Explanation**: This query returns organizational units (OUs) that contain Windows Server computers, along with the computer's name and operating system.

---

### 30. **Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.**
```cypher
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH COLLECT(c1.name) AS domainControllers
MATCH (c2:Computer {unconstraineddelegation:true})
WHERE NOT c2.name IN domainControllers
RETURN c2.name, c2.operatingsystem
ORDER BY c2.name ASC
```
Here are the Cypher queries for each of your requests, explained with appropriate context:

---

### 31. **Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local admin rights on.**
```cypher
MATCH (c1:Computer)
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer)
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer)
WITH COLLECT(c2) + COLLECT(c3) AS tempVar, c1
UNWIND tempVar AS computers
RETURN c1.name, COUNT(DISTINCT(computers))
ORDER BY COUNT(DISTINCT(computers)) DESC
```
- **Explanation**: This query identifies computers that have administrative rights (directly or through group memberships) on other computers, returning them ordered by the number of computers each one has admin rights on.

---

### 32. **Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with Unconstrained Delegation enabled.**
```cypher
MATCH (n:User {sensitive: false})-[:MemberOf*0..5]->(g:Group)-[:AdminTo]->(c:Computer)
WITH n, c
MATCH (d:Computer {unconstraineddelegation: true})-[:HasSession]->(n)
RETURN n.name AS user, c.name AS AdminTo, d.name AS TicketLocation
```
- **Explanation**: This query identifies users who are not marked as "Sensitive" and do not have the "Cannot Be Delegated" flag set. It checks where these users have administrative access to computers and have sessions on servers with unconstrained delegation enabled.

---

### 33. **Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure).**
```cypher
MATCH (u:User {sensitive: false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u, c1
MATCH (c2:Computer {unconstraineddelegation: true})-[:HasSession]->(u)
RETURN u.name AS user, COLLECT(DISTINCT(c1.name)) AS AdminTo, COLLECT(DISTINCT(c2.name)) AS TicketLocation
ORDER BY user ASC
```
- **Explanation**: This query gathers all computers where the user has admin rights and all computers with unconstrained delegation that the user has sessions on. The results are presented in lists for clarity.

---

### 34. **DOMAIN USERS is Admin of Computer**
```cypher
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer)
WHERE m.name STARTS WITH 'DOMAIN USERS'
RETURN p
```
- **Explanation**: This query finds all instances where the "DOMAIN USERS" group has administrative rights (`AdminTo`) on any computer.

---

### 35. **Find Shortest Path from DOMAIN USERS to High Value Targets**
```cypher
MATCH (g:Group), (n {highvalue: true}), p = shortestPath((g)-[*1..]->(n))
WHERE g.name STARTS WITH 'DOMAIN USERS'
RETURN p
```
- **Explanation**: This query finds the shortest path from the "DOMAIN USERS" group to any high-value targets in the domain.

---

### 36. **Find ALL Path from DOMAIN USERS to High Value Targets**
```cypher
MATCH (g:Group)
WHERE g.name STARTS WITH 'DOMAIN USERS'
MATCH (n {highvalue: true}), p = shortestPath((g)-[r*1..]->(n))
RETURN p
```
- **Explanation**: This query finds all paths (not just the shortest path) from the "DOMAIN USERS" group to high-value targets.

---

### 37. **Find all other rights Domain Users shouldn’t have**
```cypher
MATCH p=(m:Group)-[r:Owns|WriteDacl|GenericAll|WriteOwner|ExecuteDCOM|GenericWrite|AllowedToDelegate|ForceChangePassword]->(n:Computer)
WHERE m.name STARTS WITH 'DOMAIN USERS'
RETURN p
```
- **Explanation**: This query looks for any rights (e.g., `GenericAll`, `WriteDacl`, `Owns`, etc.) that the "DOMAIN USERS" group shouldn't have on computers.

---

### 38. **Kerberoastable Accounts**
```cypher
MATCH (n:User)
WHERE n.hasspn = true
RETURN n
```
- **Explanation**: This query identifies user accounts that have Service Principal Names (SPNs) set, making them potentially "kerberoastable" (i.e., vulnerable to Kerberos ticket-granting service extraction attacks).

---

### 39. **Kerberoastable Accounts member of High Value Group**
```cypher
MATCH (n:User)-[r:MemberOf]->(g:Group)
WHERE g.highvalue = true AND n.hasspn = true
RETURN n, g, r
```
- **Explanation**: This query identifies users with SPNs who are members of high-value groups, making them targets for Kerberos attacks.

---

### 40. **Find WORKSTATIONS where Domain Users can RDP To**
```cypher
MATCH p=(g:Group)-[:CanRDP]->(c:Computer)
WHERE g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server'
RETURN p
```
- **Explanation**: This query identifies workstations (i.e., non-server machines) where "DOMAIN USERS" can remotely access via RDP.

---

### 41. **Find SERVERS where Domain Users can RDP To**
```cypher
MATCH p=(g:Group)-[:CanRDP]->(c:Computer)
WHERE g.name STARTS WITH 'DOMAIN USERS' AND c.operatingsystem CONTAINS 'Server'
RETURN p
```
- **Explanation**: This query identifies servers where "DOMAIN USERS" have RDP access.

---

### 42. **Top 10 Computers with Most Admins**
```cypher
MATCH (n:User), (m:Computer), (n)-[r:AdminTo]->(m)
WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name = ''
WITH m, count(r) AS rel_count
ORDER BY rel_count DESC
LIMIT 10
MATCH p=(m)<-[r:AdminTo]-(n)
RETURN p
```
- **Explanation**: This query finds the top 10 computers with the most users with administrative access, excluding "ANONYMOUS LOGON" accounts.

---

### 43. **How many High Value Targets a Node can reach**
```cypher
MATCH p = shortestPath((n)-[*1..]->(m {highvalue: true}))
WHERE NOT n = m
RETURN DISTINCT(m.name), LABELS(m)[0], COUNT(DISTINCT(n))
ORDER BY COUNT(DISTINCT(n)) DESC
```
- **Explanation**: This query finds how many high-value targets a node (user or computer) can reach via any path, ordered by the count of reachable targets.

---

### 44. **All DA Account Sessions**
```cypher
MATCH (n:User)-[:MemberOf]->(g:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"})
MATCH p = (c:Computer)-[:HasSession]->(n)
RETURN p
```
- **Explanation**: This query finds all sessions by members of the "DOMAIN ADMINS" group on various computers.

---

### 45. **DA Sessions to NON DC**
```cypher
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group)
WHERE NOT t.name = "DOMAIN CONTROLLERS@TESTLAB.LOCAL"
WITH c AS NonDC
MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"})
RETURN DISTINCT (n.name) AS Username, COUNT(DISTINCT(NonDC)) AS Connections
ORDER BY COUNT(DISTINCT(NonDC)) DESC
```
- **Explanation**: This query finds sessions by "DOMAIN ADMINS" users on non-domain-controller computers.

---

### 46. **Count on how many non-DC machines DA have sessions**
```cypher
MATCH (c:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH c.name AS DomainControllers
MATCH p = (c2:Computer)-[:HasSession]->(u:User)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-512" AND NOT c2.name IN DomainControllers
RETURN DISTINCT(u.name), COUNT(DISTINCT(c2))
ORDER BY COUNT(DISTINCT(c2)) DESC
```
- **Explanation**: This query counts the number of non-DC machines where members of the "DOMAIN ADMINS" group have active sessions.

---

### 47. **EXCLUDE PATH (Edge) on the fly**
```cypher
MATCH (n:User), (m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}), p=shortestPath((n)-[r*1..]->(m))
WHERE ALL(x IN relationships(p) WHERE type(x) <> "AdminTo")
RETURN p
```
- **Explanation**: This query finds the shortest path from a user to "DOMAIN ADMINS" excluding any `AdminTo` relationships.
Here are the Cypher queries and explanations for your requests:

---

### 61. **Count how many Users have a path to DA**
```cypher
MATCH p = shortestPath((u:User)-[*1..]->(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
RETURN COUNT(DISTINCT u)
```
- **Explanation**: This query counts how many unique users have a path (direct or indirect) to the "DOMAIN ADMINS" group, using a shortest path approach.

---

### 62. **Percent of Users that have a path to DA**
```cypher
OPTIONAL MATCH p = shortestPath((u:User)-[*1..]->(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
OPTIONAL MATCH (uT:User)
WITH COUNT(DISTINCT uT) AS uTotal, COUNT(DISTINCT u) AS uHasPath
RETURN uHasPath / uTotal * 100 AS Percent
```
- **Explanation**: This query calculates the percentage of users who have a path to the "DOMAIN ADMINS" group. It counts both the total number of users (`uTotal`) and those who have a path (`uHasPath`), then computes the percentage.

---

### 63. **Return users with shortest paths to high-value targets by name**
```cypher
MATCH (u:User)
MATCH (g:Group {highvalue: true})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT u.name, u.enabled
ORDER BY u.name
```
- **Explanation**: This query returns users with the shortest paths to high-value targets (groups labeled as `highvalue`). The path is restricted to specific administrative and session-related rights. It returns the user name and whether the account is enabled.

---

### 64. **Return labels i.e. group, username for a given SID**
```cypher
MATCH (n {objectsid: "S-1-5-21-971234526-3761234589-3049876599-500"})
RETURN labels(n)
```
- **Explanation**: This query returns the labels (e.g., "User", "Group") for the node with the specified object SID.

---

### 65. **Return top 10 users with most Derivative local admin rights**
```cypher
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH COLLECT(c1) + COLLECT(c2) AS tempVar, u
UNWIND tempVar AS computers
RETURN u.name, COUNT(DISTINCT(computers)) AS is_admin_on_this_many_boxes
ORDER BY is_admin_on_this_many_boxes DESC
LIMIT 10
```
- **Explanation**: This query identifies users who have derivative administrative rights (directly or via group membership) on the most computers. It then returns the top 10 users with the most such rights, ordered in descending order.

---

### 66. **Find all users WITHOUT a path to DA**
```cypher
MATCH (n:User), (m:Group {name: "DOMAIN ADMINS@CONTOSO.LOCAL"})
WHERE NOT EXISTS((n)-[*]->(m))
RETURN n.name
```
- **Explanation**: This query identifies users who do not have any path (direct or indirect) to the "DOMAIN ADMINS" group.

---

### 67. **Find users that have never logged on AND their account is still active**
```cypher
MATCH (n:User)
WHERE n.lastlogontimestamp = -1.0 AND n.enabled = TRUE
RETURN n.name
ORDER BY n.name
```
- **Explanation**: This query identifies users who have never logged on (using `lastlogontimestamp = -1.0`) and whose accounts are still enabled.

---

### 68. **Find users with DCSync rights who are not members of DA**
```cypher
MATCH (n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: "TESTLAB.LOCAL"})
WITH n1, u
MATCH (n1)-[:MemberOf|GetChangesAll*1..]->(u)
WITH n1, u
MATCH p = (n1)-[:MemberOf|GetChanges|GetChangesAll*1..]->(u)
WHERE NOT (n1)-[:MemberOf*1..]->(:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"})
RETURN p
```
- **Explanation**: This query identifies users with `DCSync` rights (using `GetChanges` and `GetChangesAll`) who are not members of the "DOMAIN ADMINS" group.

---

### 69. **Show attack paths from X domain to Specific Domain**
```cypher
MATCH p = (n)-[r]->(m)
WHERE NOT n.domain = m.domain
RETURN p
```
- **Explanation**: This query finds attack paths between nodes (users or computers) in different domains. It matches all relationships where the source and target belong to different domains.

- **For specific domains**:
```cypher
MATCH p = (n {domain: "lab.local"})-[r]->(m {domain: "test.local"})
RETURN p
```
- **Explanation**: This query specifically looks for attack paths from the `lab.local` domain to the `test.local` domain.

---

### 70. **Find all computers that are NOT DCs**
```cypher
MATCH (dc:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.name STARTS WITH 'DOMAIN CONTROLLERS'
WITH COLLECT(dc) AS dcs
MATCH (c:Computer)
WHERE NOT c IN dcs
RETURN COUNT(c)
```
- **Explanation**: This query finds all computers that are not domain controllers (DCs). It first collects all domain controllers and then matches all computers that are not in this list.

- **Using `objectid`**:
```cypher
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectid ENDS WITH "-516"
WITH COLLECT(c1) AS dcs
MATCH (c:Computer)
WHERE NOT c IN dcs
RETURN COUNT(c)
```
- **Explanation**: This query uses the `objectid` suffix for domain controllers (`-516`) to identify and exclude DCs from the results.

---

### References:
- **BloodHound AD Wiki**: [BloodHound Wiki](https://github.com/BloodHoundAD/BloodHound/wiki)
- **Scoubi BloodHound Queries**: [Scoubi BloodHound Queries](https://github.com/Scoubi/BloodhoundAD-Queries)
- **BloodHound Custom Queries**: [BloodHound Custom Queries](https://github.com/awsmhacks/awsmBloodhoundCustomQueries#readme)
- **Extended BloodHound**: [Extending BloodHound](https://porterhau5.com/blog/extending-bloodhound-track-and-visualize-your-compromise/)
- **Cypher Intro**: [Cypher Intro Blog](https://blog.cptjesus.com/posts/introtocypher#building-on-top)

---
These queries help analyze various aspects of Active Directory and BloodHound-related relationships, such as user access paths, group memberships, and domain admin paths.

To also make life easier, I’ve taken the applicable queries here and made them compatible within the “Custom Queries” section in the GUI. You can download that here: https://github.com/hausec/Bloodhound-Custom-Queries

Table of Contents
GUI/Graph Queries

Console Queries

1. **Find All edges any owned user has on a computer**:
   ```cypher
   MATCH p=shortestPath((m:User)-[r]->(b:Computer)) WHERE m.owned RETURN p
   ```

2. **Find All Users with an SPN / Find all Kerberoastable Users**:
   ```cypher
   MATCH (n:User) WHERE n.hasspn=true RETURN n
   ```

3. **Find All Users with an SPN / Find all Kerberoastable Users with passwords last set > 5 years ago**:
   ```cypher
   MATCH (u:User) WHERE u.hasspn=true AND u.pwdlastset < (datetime().epochseconds - (1825 * 86400)) AND NOT u.pwdlastset IN [-1.0, 0.0]
   RETURN u.name, u.pwdlastset ORDER BY u.pwdlastset
   ```

4. **Find SPNs with keywords (swap SQL with whatever)**:
   ```cypher
   MATCH (u:User) WHERE ANY (x IN u.serviceprincipalnames WHERE toUpper(x) CONTAINS 'SQL') RETURN u
   ```

5. **Kerberoastable Users with a path to Domain Admins**:
   ```cypher
   MATCH (u:User {hasspn:true}) 
   MATCH (g:Group) WHERE g.name CONTAINS 'DOMAIN ADMINS' 
   MATCH p = shortestPath((u)-[*1..]->(g)) 
   RETURN p
   ```

6. **Find workstations a user can RDP into**:
   ```cypher
   MATCH p=(g:Group)-[:CanRDP]->(c:Computer) 
   WHERE g.objectid ENDS WITH '-513' AND NOT c.operatingsystem CONTAINS 'Server' 
   RETURN p
   ```

7. **Find servers a user can RDP into**:
   ```cypher
   MATCH p=(g:Group)-[:CanRDP]->(c:Computer) 
   WHERE g.objectid ENDS WITH '-513' AND c.operatingsystem CONTAINS 'Server' 
   RETURN p
   ```

8. **DA sessions not on a certain group (e.g. domain controllers)**:
   ```cypher
   OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) WHERE NOT t.name = 'DOMAIN CONTROLLERS@TESTLAB.LOCAL' 
   WITH c as NonDC 
   MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
   RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions 
   ORDER BY COUNT(DISTINCT(NonDC)) DESC
   ```

9. **Find all computers with Unconstrained Delegation**:
   ```cypher
   MATCH (c:Computer {unconstraineddelegation:true}) 
   RETURN c
   ```

10. **Find unsupported OSs**:
    ```cypher
    MATCH (H:Computer) 
    WHERE H.operatingsystem =~ '.*(2000|2003|2008|xp|vista|7|me)*.' 
    RETURN H
    ```

11. **Find users that logged in within the last 90 days**:
    ```cypher
    MATCH (u:User) 
    WHERE u.lastlogon < (datetime().epochseconds - (90 * 86400)) AND NOT u.lastlogon IN [-1.0, 0.0] 
    RETURN u
    ```

12. **Find users with passwords last set within the last 90 days**:
    ```cypher
    MATCH (u:User) 
    WHERE u.pwdlastset < (datetime().epochseconds - (90 * 86400)) AND NOT u.pwdlastset IN [-1.0, 0.0] 
    RETURN u
    ```

13. **Find all sessions any user in a specific domain has**:
    ```cypher
    MATCH p=(m:Computer)-[r:HasSession]->(n:User {domain: "TEST.LOCAL"}) 
    RETURN p
    ```

14. **View all GPOs**:
    ```cypher
    MATCH (n:GPO) 
    RETURN n
    ```

15. **View all GPOs that contain a keyword**:
    ```cypher
    MATCH (n:GPO) WHERE n.name CONTAINS "SERVER" 
    RETURN n
    ```

16. **View all groups that contain the word 'admin'**:
    ```cypher
    MATCH (n:Group) WHERE n.name CONTAINS "ADMIN" 
    RETURN n
    ```

17. **Find user that doesn’t require kerberos pre-authentication (AS-REP Roasting)**:
    ```cypher
    MATCH (u:User {dontreqpreauth: true}) 
    RETURN u
    ```

18. **Find a group with keywords (e.g. SQL ADMINS or SQL 2017 ADMINS)**:
    ```cypher
    MATCH (g:Group) WHERE g.name =~ '(?i).SQL.ADMIN.*' 
    RETURN g
    ```

19. **Show all high-value target groups**:
    ```cypher
    MATCH p=(n:User)-[r:MemberOf*1..]->(m:Group {highvalue:true}) 
    RETURN p
    ```

20. **Shortest paths to Domain Admins group from computers**:
    ```cypher
    MATCH (n:Computer), (m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}), p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) 
    RETURN p
    ```

21. **Shortest paths to Domain Admins group from computers excluding potential DCs**:
    ```cypher
    WITH '(?i)ldap/.*' as regex_one, '(?i)gc/.*' as regex_two 
    MATCH (n:Computer) WHERE NOT ANY(item IN n.serviceprincipalnames WHERE item =~ regex_two OR item =~ regex_two ) 
    MATCH(m:Group {name:"DOMAIN ADMINS@DOMAIN.GR"}), p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) 
    RETURN p
    ```

22. **Shortest paths to Domain Admins group from all domain groups**:
    ```cypher
    MATCH (n:Group), (m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}), p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) 
    RETURN p
    ```

23. **Shortest paths to Domain Admins group from non-privileged groups (AdminCount=false)**:
    ```cypher
    MATCH (n:Group {admincount:false}), (m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}), p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) 
    RETURN p
    ```

24. **Shortest paths to Domain Admins group from the Domain Users group**:
    ```cypher
    MATCH (g:Group) WHERE g.name =~ 'DOMAIN USERS@.*' 
    MATCH (g1:Group) WHERE g1.name =~ 'DOMAIN ADMINS@.*' 
    OPTIONAL MATCH p=shortestPath((g)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(g1)) 
    RETURN p
    ```

25. **Find interesting privileges/ACEs that have been configured to DOMAIN USERS group**:
    ```cypher
    MATCH (m:Group) WHERE m.name =~ 'DOMAIN USERS@.*' 
    MATCH p=(m)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) 
    RETURN p
    ```

26. **Shortest paths to Domain Admins group from non-privileged users (AdminCount=false)**:
    ```cypher
    MATCH (n:User {admincount:false}), (m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}), p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) 
    RETURN p
    ```

27. **Find all Edges that a specific user has against all the nodes (HasSession not included)**:
    ```cypher
    MATCH (n:User) WHERE n.name =~ 'HELPDESK@DOMAIN.GR'
    MATCH (m) WHERE NOT m.name = n.name 
    MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|
The following queries help retrieve various information about the edges and privileges of users, particularly focusing on unprivileged users or users with specific permissions. Here's a breakdown of each query with an explanation:

### 1. **Find all the Edges that any Unprivileged user (based on the `admincount:False`) has against all nodes:**
This query retrieves all shortest paths for unprivileged users (`admincount:False`) to any other node, while excluding the user's own node. It checks for several edge types such as `MemberOf`, `HasSession`, `AdminTo`, etc.

```cypher
MATCH (n:User {admincount:False}) 
MATCH (m) 
WHERE NOT m.name = n.name 
MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(m)) 
RETURN p
```

### 2. **Find interesting edges related to "ACL Abuse" that unprivileged users have against other users:**
This query retrieves shortest paths between unprivileged users and other users where there are certain privilege-related edges like `AllExtendedRights`, `ForceChangePassword`, `GenericAll`, etc.

```cypher
MATCH (n:User {admincount:False}) 
MATCH (m:User) 
WHERE NOT m.name = n.name 
MATCH p=allShortestPaths((n)-[r:AllExtendedRights|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner*1..]->(m)) 
RETURN p
```

### 3. **Find interesting edges related to "ACL Abuse" that unprivileged users have against computers:**
This query searches for paths where unprivileged users have certain rights over computers, focusing on edges like `AllExtendedRights`, `GenericAll`, `GenericWrite`, `CanRDP`, etc.

```cypher
MATCH (n:User {admincount:False}) 
MATCH p=allShortestPaths((n)-[r:AllExtendedRights|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|AdminTo|CanRDP|ExecuteDCOM|ForceChangePassword*1..]->(m:Computer)) 
RETURN p
```

### 4. **Find if unprivileged users have rights to add members into groups:**
This query retrieves paths where unprivileged users have `AddMember` rights, indicating they can add members to groups.

```cypher
MATCH (n:User {admincount:False}) 
MATCH p=allShortestPaths((n)-[r:AddMember*1..]->(m:Group)) 
RETURN p
```

### 5. **Find the active user sessions on all domain computers:**
This query identifies user sessions across all domain computers using `MemberOf` relationships to groups and `HasSession` edges between computers and users.

```cypher
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) 
MATCH p2=(c:Computer)-[*1]->(u1) 
RETURN p2
```

### 6. **Find all the privileges (edges) of domain users against the domain computers:**
This query retrieves all edges between domain users and computers, excluding the `HasSession` edge, but including privileges like `CanRDP`, `AdminTo`, etc.

```cypher
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) 
MATCH p2=(u1)-[*1]->(c:Computer) 
RETURN p2
```

### 7. **Find only the `AdminTo` privileges (edges) of domain users against domain computers:**
This query specifically focuses on paths where domain users have `AdminTo` rights over domain computers.

```cypher
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) 
MATCH p2=(u1)-[:AdminTo*1..]->(c:Computer) 
RETURN p2
```

### 8. **Find only the `CanRDP` privileges (edges) of domain users against domain computers:**
This query looks for paths where domain users have `CanRDP` rights to domain computers.

```cypher
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) 
MATCH p2=(u1)-[:CanRDP*1..]->(c:Computer) 
RETURN p2
```

### 9. **Display a specific user with constrained delegation and his targets where he is allowed to delegate:**
This query identifies a specific user (by their username) and retrieves the computers to which they are allowed to delegate with constrained delegation.

```cypher
MATCH (u:User {name:'USER@DOMAIN.GR'}),(c:Computer),
p=((u)-[r:AllowedToDelegate]->(c)) 
RETURN p
```

Console Queries

Here are the Neo4j Cypher queries for the tasks you requested, each with a clear description of what it does:

### 1. **Find what groups can RDP:**
This query retrieves all groups that have the `CanRDP` privilege over domain computers.

```cypher
MATCH p=(m:Group)-[r:CanRDP]->(n:Computer) 
RETURN m.name, n.name 
ORDER BY m.name
```

### 2. **Find what groups can reset passwords:**
This query returns all groups that can reset passwords (via the `ForceChangePassword` privilege) over users.

```cypher
MATCH p=(m:Group)-[r:ForceChangePassword]->(n:User) 
RETURN m.name, n.name 
ORDER BY m.name
```

### 3. **Find what groups have local admin rights:**
This query retrieves all groups that have the `AdminTo` privilege over domain computers, indicating they have local administrator rights.

```cypher
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) 
RETURN m.name, n.name 
ORDER BY m.name
```

### 4. **Find what users have local admin rights:**
This query identifies all users who have `AdminTo` rights over domain computers.

```cypher
MATCH p=(m:User)-[r:AdminTo]->(n:Computer) 
RETURN m.name, n.name 
ORDER BY m.name
```

### 5. **List the groups of all owned users:**
This query lists the groups to which all "owned" users belong.

```cypher
MATCH (m:User) 
WHERE m.owned=TRUE 
WITH m 
MATCH p=(m)-[:MemberOf*1..]->(n:Group) 
RETURN m.name, n.name 
ORDER BY m.name
```

### 6. **List the unique groups of all owned users:**
This query lists the unique groups associated with "owned" users.

```cypher
MATCH (m:User) 
WHERE m.owned=TRUE 
WITH m 
MATCH (m)-[r:MemberOf*1..]->(n:Group) 
RETURN DISTINCT(n.name)
```

### 7. **All active DA (Domain Admins) sessions:**
This query finds all active sessions where users are members of the "Domain Admins" group (whose `objectid` ends with `-512`).

```cypher
MATCH (n:User)-[:MemberOf*1..]->(g:Group) 
WHERE g.objectid ENDS WITH '-512' 
MATCH p = (c:Computer)-[:HasSession]->(n) 
RETURN p
```

### 8. **Find all active sessions a member of a specific group has:**
This query finds all active sessions of users who are members of the "DOMAIN ADMINS@TESTLAB.LOCAL" group.

```cypher
MATCH (n:User)-[:MemberOf*1..]->(g:Group {name:'DOMAIN ADMINS@TESTLAB.LOCAL'}) 
MATCH p = (c:Computer)-[:HasSession]->(n) 
RETURN p
```

### 9. **Can an object from domain ‘A’ do anything to an object in domain ‘B’:**
This query checks whether objects from domain "TEST.LOCAL" have any interactions (edges) with objects in domain "LAB.LOCAL."

```cypher
MATCH (n {domain:"TEST.LOCAL"})-[r]->(m {domain:"LAB.LOCAL"}) 
RETURN LABELS(n)[0],n.name,TYPE(r),LABELS(m)[0],m.name
```

### 10. **Find all connections to a different domain/forest:**
This query retrieves all relationships between objects from different domains or forests.

```cypher
MATCH (n)-[r]->(m) 
WHERE NOT n.domain = m.domain 
RETURN LABELS(n)[0],n.name,TYPE(r),LABELS(m)[0],m.name
```

### 11. **Find all users with an SPN (Service Principal Name)/Kerberoastable Users with passwords last set > 5 years ago:**
This query finds users with SPNs who have not updated their password in over 5 years.

```cypher
MATCH (u:User) 
WHERE u.hasspn=true 
AND u.pwdlastset < (datetime().epochseconds - (1825 * 86400)) 
AND NOT u.pwdlastset IN [-1.0, 0.0] 
RETURN u.name, u.pwdlastset 
ORDER BY u.pwdlastset
```

### 12. **Kerberoastable Users with most privileges:**
This query finds users with SPNs and how many distinct computers they have access to based on privileges (counting `AdminTo`).

```cypher
MATCH (u:User {hasspn:true}) 
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer) 
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) 
WITH u,COLLECT(c1) + COLLECT(c2) AS tempVar 
UNWIND tempVar AS comps 
RETURN u.name, COUNT(DISTINCT(comps)) 
ORDER BY COUNT(DISTINCT(comps)) DESC
```

### 13. **Find users that logged in within the last 90 days:**
This query finds users who logged in within the last 90 days (or any custom threshold).

```cypher
MATCH (u:User) 
WHERE u.lastlogon > (datetime().epochseconds - (90 * 86400)) 
AND NOT u.lastlogon IN [-1.0, 0.0] 
RETURN u.name, u.lastlogon 
ORDER BY u.lastlogon
```

### 14. **Find users with passwords last set within the last 90 days:**
This query finds users who set their password within the last 90 days (or any custom threshold).

```cypher
MATCH (u:User) 
WHERE u.pwdlastset > (datetime().epochseconds - (90 * 86400)) 
AND NOT u.pwdlastset IN [-1.0, 0.0] 
RETURN u.name, u.pwdlastset 
ORDER BY u.pwdlastset
```

### 15. **List users and their login times + password last set times in human-readable format:**
This query lists users and their login times and password last set times in a human-readable format.

```cypher
MATCH (n:User) 
WHERE n.enabled = TRUE 
RETURN n.name, datetime({epochSeconds: toInteger(n.pwdlastset)}), datetime({epochSeconds: toInteger(n.lastlogon)}) 
ORDER BY n.pwdlastset
```

### 16. **Find constrained delegation:**
This query lists users allowed to delegate to computers, indicating constrained delegation rights.

```cypher
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer) 
RETURN u.name, COUNT(c) 
ORDER BY COUNT(c) DESC
```

### 17. **View OUs based on member count:**
This query returns Organizational Units (OUs) sorted by the number of computers they contain.

```cypher
MATCH (o:OU)-[:Contains]->(c:Computer) 
RETURN o.name, o.guid, COUNT(c) 
ORDER BY COUNT(c) DESC
```

### 18. **Return each OU that has a Windows Server in it:**
This query finds OUs that contain computers with operating systems starting with "Windows Server."

```cypher
MATCH (o:OU)-[:Contains]->(c:Computer) 
WHERE toUpper(c.operatingsystem) STARTS WITH "WINDOWS SERVER" 
RETURN o.name
```

### 19. **Find computers that allow unconstrained delegation that aren’t domain controllers:**
This query identifies computers with unconstrained delegation, excluding domain controllers.

```cypher
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group) 
WHERE g.objectsid ENDS WITH '-516' 
WITH COLLECT(c1.name) AS domainControllers 
MATCH (c2:Computer {unconstraineddelegation:true}) 
WHERE NOT c2.name IN domainControllers 
RETURN c2.name, c2.operatingsystem 
ORDER BY c2.name ASC
```

### 20. **Find the number of principals with control of a “high value” asset where the principal does not belong to a “high value” group:**
This query finds principals (users or groups) that have control over a "high value" asset but do not belong to a high-value group themselves.

```cypher
MATCH (n {highvalue:true}) 
OPTIONAL MATCH (m1)-[r:isacl]->(n) 
WHERE NOT (m1)-[:MemberOf*1..]->(:Group {highvalue:true}) 
OPTIONAL MATCH (m2)-[:MemberOf*1..]->(:Group)-[r:isacl]->(n) 
WHERE NOT (m2)-[:MemberOf*1..]->(:Group {highvalue:true}) 
WITH n,COLLECT(m1) + COLLECT(m2) AS tempVar 
UNWIND tempVar AS controllers 
RETURN n.name, COUNT(DISTINCT(controllers)) 
ORDER BY COUNT(DISTINCT(controllers)) DESC
```
Enumerate all properties (In Console)   

Here are the Cypher queries for the tasks you requested:

### 1. **Enumerate all properties (in Console):**
This query retrieves all properties of computers in the domain.

```cypher
MATCH (n:Computer) 
RETURN properties(n)
```

### 2. **Match users that are not AdminCount 1, have GenericAll, and no local admin:**
This query finds users who have the `GenericAll` privilege but are not `AdminCount` 1 and do not have local admin rights.

```cypher
MATCH (u:User)-[:GenericAll]->(c:Computer) 
WHERE NOT u.admincount AND NOT (u)-[:AdminTo]->(c) 
RETURN u.name, c.name
```

### 3. **What permissions does Everyone/Authenticated users/Domain users/Domain computers have:**
This query finds what permissions groups like "Everyone", "Authenticated Users", "Domain Users", and "Domain Computers" have.

```cypher
MATCH p=(m:Group)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChanges|GetChangesAll|HasSession|Owns|ReadLAPSPassword|SQLAdmin|TrustedBy|WriteDACL|WriteOwner|AddAllowedToAct|AllowedToAct]->(t) 
WHERE m.objectsid ENDS WITH '-513' OR m.objectsid ENDS WITH '-515' OR m.objectsid ENDS WITH 'S-1-5-11' OR m.objectsid ENDS WITH 'S-1-1-0' 
RETURN m.name, TYPE(r), t.name, t.enabled
```

### 4. **Find computers with descriptions and display them (sensitive data in descriptions):**
This query returns computers with descriptions (sometimes they contain sensitive data like passwords).

```cypher
MATCH (c:Computer) 
WHERE c.description IS NOT NULL 
RETURN c.name, c.description
```

### 5. **Return the name of every computer with an SPN containing "MSSQL":**
This query lists computers whose SPNs contain the string "MSSQL".

```cypher
MATCH (c:Computer) 
WHERE ANY(x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS 'MSSQL') 
RETURN c.name, c.serviceprincipalnames 
ORDER BY c.name ASC
```

### 6. **Find any computer that is NOT a domain controller and is trusted to perform unconstrained delegation:**
This query finds computers that are not domain controllers but are trusted for unconstrained delegation.

```cypher
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group) 
WHERE g.objectid ENDS WITH '-516' 
WITH COLLECT(c1.name) AS domainControllers 
MATCH (c2:Computer {unconstraineddelegation:true}) 
WHERE NOT c2.name IN domainControllers 
RETURN c2.name, c2.operatingsystem 
ORDER BY c2.name ASC
```

### 7. **Find every computer account that has local admin rights on other computers (sorted by count):**
This query finds computer accounts with local admin rights on other computers and returns them in descending order based on the number of computers they have admin rights on.

```cypher
MATCH (c1:Computer) 
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer) 
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer) 
WITH COLLECT(c2) + COLLECT(c3) AS tempVar, c1 
UNWIND tempVar AS computers 
RETURN c1.name AS COMPUTER, COUNT(DISTINCT(computers)) AS ADMIN_TO_COMPUTERS 
ORDER BY COUNT(DISTINCT(computers)) DESC
```

### 8. **Find every computer that has local admin rights on other computers and display these computers:**
This query displays computers with local admin rights on other computers.

```cypher
MATCH (c1:Computer) 
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer) 
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer) 
WITH COLLECT(c2) + COLLECT(c3) AS tempVar, c1 
UNWIND tempVar AS computers 
RETURN c1.name AS COMPUTER, COLLECT(DISTINCT(computers.name)) AS ADMIN_TO_COMPUTERS 
ORDER BY c1.name
```

### 9. **Get the names of the computers without admins (sorted alphabetically):**
This query finds computers that do not have local administrators.

```cypher
MATCH (n)-[r:AdminTo]->(c:Computer) 
WITH COLLECT(c.name) as compsWithAdmins 
MATCH (c2:Computer) 
WHERE NOT c2.name IN compsWithAdmins 
RETURN c2.name 
ORDER BY c2.name ASC
```

### 10. **Show computers (excluding Domain Controllers) where Domain Admins are logged in:**
This query lists computers where Domain Admins are logged in, excluding domain controllers.

```cypher
MATCH (n:User)-[:MemberOf*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}) 
WITH n as privusers
```

### 11. **Find the percentage of computers with a path to Domain Admins:**
This query finds the percentage of computers that have a path to the Domain Admins group.

```cypher
MATCH (totalComputers:Computer {domain:'DOMAIN.GR'}) 
MATCH p=shortestPath((ComputersWithPath:Computer {domain:'DOMAIN.GR'})-[r*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'})) 
WITH COUNT(DISTINCT(totalComputers)) AS totalComputers, COUNT(DISTINCT(ComputersWithPath)) AS ComputersWithPath 
RETURN 100.0 * ComputersWithPath / totalComputers AS percentComputersToDA
```

### 12. **Find on each computer who can RDP (searching only enabled users):**
This query finds which users can RDP to each computer, focusing on enabled users.

```cypher
MATCH (c:Computer) 
OPTIONAL MATCH (u:User)-[:CanRDP]->(c) 
WHERE u.enabled=true 
OPTIONAL MATCH (u1:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c) 
WHERE u1.enabled=true 
WITH COLLECT(u) + COLLECT(u1) AS tempVar, c 
UNWIND tempVar AS users 
RETURN c.name AS COMPUTER, COLLECT(DISTINCT(users.name)) AS USERS 
ORDER BY USERS DESC
```

### 13. **Find on each computer the number of users with admin rights (local admins) and display the users:**
This query finds the number of users with admin rights on each computer and lists the users.

```cypher
MATCH (c:Computer) 
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c) 
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c) 
WITH COLLECT(u1) + COLLECT(u2) AS TempVar, c 
UNWIND TempVar AS Admins 
RETURN c.name AS COMPUTER, COUNT(DISTINCT(Admins)) AS ADMIN_COUNT, COLLECT(DISTINCT(Admins.name)) AS USERS 
ORDER BY ADMIN_COUNT DESC
```

### 14. **Active Directory group with default privileged rights on domain users and groups, plus the ability to logon to Domain Controllers:**
This query finds Active Directory groups with default privileged rights on domain users and groups and the ability to log on to Domain Controllers.

```cypher
MATCH (g:Group)-[:MemberOf*1..]->(u:User) 
WHERE g.name='DOMAIN ADMINS' 
RETURN g.name, COLLECT(u.name) AS users, COUNT(u) AS userCount
```

These are various queries written for Cypher, the query language for querying graph databases like Neo4j, that focus on Active Directory and its various relationships. Here's a breakdown of the various queries and their functions:

### 1. **Find users that are members of "ACCOUNT OPERATORS@DOMAIN.GR":**
```cypher
MATCH (u:User)-[r1:MemberOf*1..]->(g1:Group {name:'ACCOUNT OPERATORS@DOMAIN.GR'}) 
RETURN u.name
```
This query retrieves the users who are members of the "ACCOUNT OPERATORS" group in a specific domain.

### 2. **Find domain Groups that are Admins to computers:**
```cypher
MATCH (g:Group) 
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) 
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) 
WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar 
UNWIND tempVar AS computers 
RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights
```
This query finds which domain groups (including nested groups) have administrative access to which computers.

### 3. **Find domain Groups (excluding Domain Admins and Enterprise Admins) that are Admins to computers:**
```cypher
MATCH (g:Group) 
WHERE NOT (g.name =~ '(?i)domain admins@.*' OR g.name =~ "(?i)enterprise admins@.*") 
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) 
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) 
WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar 
UNWIND tempVar AS computers 
RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights
```
This query excludes "Domain Admins" and "Enterprise Admins" and finds other groups with administrative rights on computers.

### 4. **Find non-privileged domain Groups (admincount=false) that are Admins to computers:**
```cypher
MATCH (g:Group) 
WHERE g.admincount=false 
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) 
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) 
WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar 
UNWIND tempVar AS computers 
RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights
```
This query finds groups with no administrative count that are admins to computers.

### 5. **Find the most privileged groups on the domain (groups that are admins to computers, including nested groups):**
```cypher
MATCH (g:Group) 
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) 
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) 
WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar 
UNWIND tempVar AS computers 
RETURN g.name AS GroupName,COUNT(DISTINCT(computers)) AS AdminRightCount 
ORDER BY AdminRightCount DESC
```
This query identifies the groups that have the highest number of computers with administrative access.

### 6. **Find the number of computers without local admins:**
```cypher
MATCH (n)-[r:AdminTo]->(c:Computer) 
WITH COLLECT(c.name) as compsWithAdmins 
MATCH (c2:Computer) 
WHERE NOT c2.name in compsWithAdmins 
RETURN COUNT(c2)
```
This query counts the number of computers that do not have any local administrators.

### 7. **Find groups with the most local admins (explicit or derivative):**
```cypher
MATCH (g:Group) 
WITH g 
OPTIONAL MATCH (g)-[r:AdminTo]->(c1:Computer) 
WITH g, COUNT(c1) as explicitAdmins 
OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c2:Computer) 
WITH g, explicitAdmins, COUNT(DISTINCT(c2)) as unrolledAdmins 
RETURN g.name, explicitAdmins, unrolledAdmins, explicitAdmins + unrolledAdmins as totalAdmins 
ORDER BY totalAdmins DESC
```
This query finds groups with the most local administrators, considering both explicit and inherited admin rights.

### 8. **List the sessions for each user in a specified organizational unit (OU) GUID:**
```cypher
MATCH p=(o:OU {guid:'045939B4-3FA8-4735-YU15-7D61CFOU6500'})-[r:Contains*1..]->(u:User) 
MATCH (c:Computer)-[rel:HasSession]->(u) 
RETURN u.name, c.name
```
This query lists all sessions from users in a specific Organizational Unit (OU), given its GUID.

### 9. **List users with sessions on computers tagged as "Tier 2" with local admin privileges on "Tier 1" computers:**
```cypher
MATCH (c:Computer)-[rel:HasSession]->(u:User) 
WHERE u.tier_1_user = true AND c.tier_2_computer = true 
RETURN u.name, u.displayname, TYPE(rel), c.name, labels(c), c.enabled
```
This query identifies users with sessions on "Tier 2" computers and their associated administrative access on "Tier 1" computers.

### 10. **Find users with constrained delegation permissions:**
```cypher
MATCH (u:User) WHERE u.allowedtodelegate IS NOT NULL 
RETURN u.name, u.allowedtodelegate
```
This query identifies users who have permissions to delegate actions to other users or computers, potentially useful in the context of Kerberos authentication.

# *BH Observation Queries&*
```
It seems like you are working with a complex Cypher query setup for extracting detailed user, group, and computer privilege relationships from a Neo4j database. Here’s a brief breakdown of what the provided Cypher queries do:

### 1. **Targetable Users**
   - Identifies users with paths to the `DOMAIN ADMINS` group, showing their name, status, display name, email, and designation.
   - Extracts additional user relationships to check who is connected to the `DOMAIN ADMINS` group via sessions.

   **Key queries:**
   ```cypher
   MATCH p=shortestPath((u:User)-[*1..]->(m:Group {name: "DOMAIN ADMINS@GUWW.NET"}))
   RETURN DISTINCT(u.name) as Name, (u.enabled) as Status, (u.displayname) as DisplayName, (u.email) as Email, (u.title) As Designation ORDER BY Designation, Email
   ```

### 2. **Targetable Computers**
   - Identifies computers that are connected to `DOMAIN ADMINS` group members and have an enabled status.

   **Key queries:**
   ```cypher
   MATCH p=shortestPath((c:Computer)-[*1..]->(m:Group {name: "DOMAIN ADMINS@GUWW.NET"})) 
   WHERE c.enabled=true
   RETURN DISTINCT (c.name) as Computer_name, (c.enabled) as Status, (c.operatingsystem) as Operatingsystem, (c.serviceprincipalnames) as SPN
   ```

### 3. **EOL (End of Life) Operating Systems**
   - Identifies legacy OS machines that might pose security risks due to outdated support. These include older versions like Windows XP, Vista, and Server 2008.

   **Key queries:**
   ```cypher
   MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' AND H.enabled = true
   RETURN H.name AS Computer_Name, H.operatingsystem AS Operatingsystem, H.serviceprincipalnames AS ServicesRunning
   ```

### 4. **Admin Rights: User and Group Level**
   - Identifies users and groups with administrative privileges on various computers by tracking "AdminTo" relationships.
   - Provides a detailed breakdown of direct and derived administrative access.

   **Key queries:**
   ```cypher
   MATCH (u1:User)
   WITH u1
   OPTIONAL MATCH (u1)-[r:AdminTo]->(c:Computer)
   WITH u1, COUNT(c) as expAdmin
   OPTIONAL MATCH (u1)-[r:MemberOf*1..]->(a:User)-[r2:AdminTo]->(c:Computer)
   WITH u1, expAdmin, COUNT(DISTINCT(c)) as unrolledAdmin
   RETURN u1.name as User_Name, expAdmin, unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
   ORDER BY totalAdmin DESC
   ```

   Similar logic applies for groups, checking for direct and derived administrative access.

### 5. **Delegated Local Admin Rights (User and Group)**
   - Finds relationships where groups or users are delegated local admin rights on computers.

   **Key queries:**
   ```cypher
   MATCH p=(u1:User)-[r:AdminTo]->(n:Computer)
   RETURN u1.name, n.name

   MATCH p=(m:Group)-[r:AdminTo]->(n:Computer)
   MATCH (u1:User)-[r:AdminTo]->(n:Computer)
   RETURN m.name, n.name
   ```

### 6. **Derivative Local Admin Rights**
   - Finds paths that show indirect admin access due to group memberships or sessions (users/groups with indirect admin rights through hierarchies).

   **Key queries:**
   ```cypher
   MATCH p=shortestPath((m:User)-[r:HasSession|AdminTo|MemberOf*1..]->(n:Computer)) 
   RETURN p
   ```

	List of Users to LocalAdmins (Explicit)
	MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN u1.name,n.name
	 
	List of Users to Local Admins with all the properties
	MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN p
	 
	Explicit and unrolled users to computes
	MATCH (u1:User) WITH g OPTIONAL MATCH (u1)-[r:AdminTo]->(c1:Computer) WITH u1,COUNT(c1) as explicitAdmins OPTIONAL MATCH (u1)-[r:MemberOf*1..]->(a:User)-[r2:AdminTo]->(c2:Computer) WITH u1,explicitAdmins,COUNT(DISTINCT(c2)) as unrolledAdmins RETURN u1.name,explicitAdmins,unrolledAdmins, explicitAdmins + unrolledAdmins as totalAdmins ORDER BY totalAdmins DESC
	 
	List of users with delegated Local Admins
	MATCH p = (u:User)-[r1:MemberOf]->(g:Group)-[r2:AdminTo]->(n:Computer) RETURN u.name,type(r1),g.name,n.name


It looks like you are working on a series of Cypher queries for analyzing user and group privileges, system configurations, and various relationships in a Neo4j graph database. These queries seem focused on security and auditing purposes, such as identifying potential attack paths, old or vulnerable systems, high-privilege users, and system configurations.

### Breakdown of the Key Queries

#### **6. Non-Domain Controller with Unconstrained Delegation**
   - This query identifies computers that are configured for unconstrained delegation but are **not domain controllers** (DCs). It ensures that any computer with this dangerous configuration is excluded if it's a domain controller.
   ```cypher
   MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
   WHERE g.objectsid ENDS WITH "-516"  // Identifying Domain Controllers
   WITH COLLECT(c1.name) AS domainControllers
   MATCH (c2:Computer {unconstraineddelegation:true})
   WHERE NOT c2.name IN domainControllers
   RETURN c2.name, c2.operatingsystem, c2.serviceprincipalnames
   ORDER BY c2.name ASC
   ```

#### **7. Users with Passwords Set More Than 180 Days Ago**
   - Retrieves users whose passwords were last set more than 180 days ago, excluding the system accounts where `pwdlastset` might be `-1` or `0` (which typically indicate system accounts that never had their password set).
   ```cypher
   MATCH (u:User)
   WHERE u.pwdlastset < (datetime().epochseconds - (180 * 86400)) 
   AND NOT u.pwdlastset IN [-1.0, 0.0] 
   RETURN u, u.name
   ```

#### **8. Users Who Have Never Logged In but Are Still Active**
   - Identifies active users who have **never logged on** (based on the `lastlogontimestamp` being `-1`).
   ```cypher
   MATCH (n:User) 
   WHERE n.lastlogontimestamp=-1.0 AND n.enabled=TRUE
   RETURN n.name ORDER BY n.name
   ```

#### **9. SPN (Service Principal Name) for MSSQL**
   - This query lists computers that have an SPN containing `MSSQL`, which typically indicates systems running Microsoft SQL Server.
   ```cypher
   MATCH (c:Computer)
   WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
   RETURN c.name, c.serviceprincipalnames
   ORDER BY c.name ASC
   ```

#### **10. SMB Shares in Description Fields**
   - This identifies nodes where the description field contains `\\`, which is commonly used in SMB (Server Message Block) share names.
   ```cypher
   MATCH (n) 
   WHERE n.description CONTAINS '\\\\' 
   RETURN n.name, n.description
   ```

#### **11. Groups with CanRDP Permission on Computers**
   - Retrieves groups and users who have the "CanRDP" (Remote Desktop Protocol) permission on computers.
   ```cypher
   MATCH p=(m:Group)-[r:CanRDP]->(n:Computer) 
   RETURN m.name, n.name
   MATCH p=(u1:User)-[r:CanRDP]->(n:Computer) 
   RETURN u1.name, n.name
   ```

#### **12. ALC (Access Control List) Permissions**
   - This set of queries covers users, groups, and computers with various extended rights or permissions (like `HasSession`, `AdminTo`, and many others).
   - It checks for group-based, user-based, and computer-based permissions, such as `GenericAll`, `GenericWrite`, `AllExtendedRights`, and others. This is crucial for detecting potential privilege escalation paths.
   
   Example for groups with session rights on computers:
   ```cypher
   MATCH p=(m:Group)-[r:AllExtendedRights]->(n:Computer) 
   RETURN m.name as Group_Name, type(r) as ACL_Permission, n.name as Computer_Name
   ```

   And for users with session rights:
   ```cypher
   MATCH p=(u1:User)-[r:AllExtendedRights]->(n:Computer) 
   RETURN u1.name as User_Name, type(r) as ACL_Permission, n.name as Computer_Name
   ```

### Suggestions and Improvements

- **Indexing:** Ensure that key properties (e.g., `name`, `enabled`, `pwdlastset`, `lastlogontimestamp`, `serviceprincipalnames`, etc.) have indexes on them to improve the performance of the queries, especially as the database grows.
  
- **Handling Legacy OS:** If you are looking for legacy OS systems (e.g., Windows XP, 2000), it might be beneficial to set up a specific group or flag on systems that are known to run legacy OS versions. This can be cross-checked with security policies.

- **Output Clarification:** For some queries (like **7** and **8**), you might want to include more details, such as the user's full name, email, or other attributes for further actionability.

- **Security Best Practices:** Queries checking for `unconstraineddelegation` or `CanRDP` permissions should be reviewed for security best practices and risk mitigation. It's crucial to ensure that only trusted and authorized users/groups/computers have these rights.

### Additional Example Queries

1. **Check for Computers with SMB Shares and Service Principal Names (SPNs)**
   This could identify potential vulnerabilities related to services like MSSQL that are exposed through SMB:
   ```cypher
   MATCH (c:Computer) 
   WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
   MATCH (n:Computer)
   WHERE ANY (x IN n.serviceprincipalnames WHERE toUpper(x) CONTAINS "HOST")
   RETURN c.name, n.name, n.serviceprincipalnames
   ```

2. **Users with Both Admin Rights and CanRDP Permissions**
   Identifying users who have both administrative rights and remote desktop access:
   ```cypher
   MATCH p=(u:User)-[r:AdminTo]->(n:Computer)
   MATCH p2=(u)-[r2:CanRDP]->(n)
   RETURN u.name, n.name, type(r) as Admin_Permission, type(r2) as RDP_Permission
   ```

