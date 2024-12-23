*****  DOMAIN USERS is Admin of Computer 
MATCH p=(m:Group)-[:AdminTo]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
*****  

******* ShortestPath From Domain Users to Domain Admins
MATCH p=ShortestPath((n {name:"DOMAIN USERS@TESTLAB.LOCAL"})
-[*1..]->(m {name: "DOMAIN ADMINS@TESTLAB.LOCAL"})) 
RETURN p
*******

******* Find Shortest Path from DOMAIN USERS to High Value Targets
MATCH (g:Group),(n {highvalue:true}),p=shortestPath((g)-[*1..]->(n)) 
WHERE g.name STARTS WITH 'DOMAIN USERS' 
RETURN p
********

******* Find ALL Path from DOMAIN USERS to High Value Targets
MATCH (g:Group) WHERE g.name STARTS WITH 'DOMAIN USERS'  MATCH (n {highvalue:true}),p=shortestPath((g)-[r*1..]->(n)) return p
*****

***** Find all other right Domain Users shouldn’t have
MATCH p=(m:Group)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
*****

***** Kerberoastable Accounts
MATCH (n:User)WHERE n.hasspn=true RETURN n
*****

***** Kerberoastable Accounts member of High Value Group
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
******

****** Find WORKSTATIONS where Domain Users can RDP To
match p=(g:Group)-[:CanRDP]->(c:Computer) where g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server' return p
*******

******* Find SERVERS where Domain Users can RDP To
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' 
AND c.operatingsystem CONTAINS 'Server' 
return p
*******

******* Top 10 Computers with Most Admins
MATCH (n:User),(m:Computer), (n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m, count(r) as rel_count order by rel_count desc LIMIT 10 MATCH p=(m)<-[r:AdminTo]-(n) RETURN p
*******

******* How many High Value Target a Node can reach
MATCH p = shortestPath((n)-[*1..]->(m {highvalue:true}))
WHERE NOT n = m
RETURN DISTINCT(m.name),LABELS(m)[0],COUNT(DISTINCT(n))
ORDER BY COUNT(DISTINCT(n)) DESC
*******
 
******* All DA Account Sessions
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
MATCH p = (c:Computer)-[:HasSession]->(n) 
return p
********

******** DA Sessions to NON DC - NEW WORKING VERSION
MATCH (dc:Computer)-[:MemberOf*1..]->(g:Group) 
WHERE g.name CONTAINS "DOMAIN CONTROLLERS" 
WITH COLLECT(dc.name) as dcs 
MATCH (c:Computer) WHERE NOT c.name in dcs 
MATCH p=(c)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group)  
WHERE g.name STARTS WITH "DOMAIN ADMINS"  
RETURN n.name as Username
********

******** Count on how many non DC machines DA have sessions - Original query by Waldo (Not working afaik) Probably missing a COLLECT after WITH
MATCH (c:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH c.name as DomainControllers
MATCH p = (c2:Computer)-[:HasSession]->(u:User)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-512" AND NOT c2.name in DomainControllers
RETURN DISTINCT(u.name),COUNT(DISTINCT(c2))
ORDER BY COUNT(DISTINCT(c2)) DESC
*********

********* EXCLUDE PATH (Edge) on the fly
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((n)-[r*1..]->(m)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
RETURN p
**********

********** Set SPN to a User
MATCH (n:User {name:"JNOA00093@TESTLAB.LOCAL"}) SET n.hasspn=true
**********

********** Add DOMAIN USERS as Admin to COMPxxxx
MERGE (n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}) WITH n MERGE (m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) WITH n,m MERGE (n)-[:AdminTo]->(m)
**********

********** Test our New Relationship
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
RETURN p
********** Try to replace AdminTo by 1**

********** Delete a Relationship
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->
(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
DELETE r
**********

********** Shortest Path from Domain Users to Domain Admins
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[*1..]->(n)) 
return p
**********

********** Shortest Path from Domain Users to Domain Admins (No AdminTo)
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
return p
**********

********** Find Admin Group not tagged highvalue
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
RETURN g.name
**********

********** Set Admin Group as highvalue
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
SET g.highvalue=true
**********

********** Remore Admin User highvalue
MATCH p=(g:Group {highvalue: true})<-[:MemberOf]-(u:User)
WHERE g.name CONTAINS "ADMIN"
SET g.highvalue = NULL
**********
If you want to enumerate all available properties : 
Match (n:Computer) return properties(n)
********** List all computers with unconstraineddelegation
MATCH (c:Computer {unconstraineddelegation:true})
return c.name
**********

********** Computer where the most users can RDP to 
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COUNT(DISTINCT(users))
ORDER BY COUNT(DISTINCT(users)) DESC
**********

********** Average Length of Path
MATCH (g:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'})
MATCH p = shortestPath((u:User)-[r:AdminTo|MemberOf|HasSession*1..]->(g))
WITH EXTRACT(n in NODES(p) | LABELS(n)[0]) as pathNodes
WITH FILTER(x IN pathNodes WHERE x = "Computer") as filteredPathNodes
RETURN AVG(LENGTH(filteredPathNodes))
**********
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name

********** Count how many Users have a path to DA
MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
RETURN COUNT (DISTINCT(u))
**********

********** % of how many Users have a path to DA
OPTIONAL MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
OPTIONAL MATCH (uT:User)
WITH COUNT (DISTINCT(uT)) as uTotal, COUNT (DISTINCT(u)) as uHasPath
RETURN uHasPath *100 / uTotal as Percent
**********

********** Find users WITHOUT a path to DA
MATCH (n:User),(m:Group) WHERE NOT EXISTS ((n)-[*]->(m {name:"DOMAIN ADMINS@TESTLAB.LOCAL"})) RETURN n.name
********** WARNING - This is extremly long
********** Find High Value Users that are NOT in the Protected User group
MATCH (prot:User)-[:MemberOf*1..]->(g:Group) 
WHERE g.name = "PROTECTED USERS@SYNTAX.NET"
WITH COLLECT(prot) AS protected_user 
MATCH p = (np:User)-[:MemberOf*1..]->(m:Group)
WHERE  m.highvalue = True AND NOT np IN protected_user 
RETURN np
**********

********** Find "Easy To Exploit" Path to high value target from Owned 
MATCH p=shortestpath((s:User {owned: true})-[:MemberOf|AdminTo|Owns|ForceChangePassword|AddMember*1..]->(t {highvalue: true}))
RETURN p

From <https://raw.githubusercontent.com/Scoubi/BloodhoundAD-Queries/master/BH%20Red2Blue.txt> 
1. Get a count of computers that do not have admins
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN COUNT(c2)

2. Get the names of computers without admins, sorted in alphabetical order
MATCH (n)-[r:AdminTo]->(c:Computer)
WITH COLLECT(c.name) as compsWithAdmins
MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins
RETURN c2.name
ORDER BY c2.name ASC
3. Return a list of users who have admin rights on at least one system either explicitly or through group membership
MATCH (u:User)-[r:AdminTo|MemberOf*1..]->(c:Computer
RETURN u.name

4. Return username and number of computers that username is admin for, for top N users

MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
RETURN n,c
ORDER BY c DESC LIMIT 5
5. Show all users that are administrator on more than one machine
MATCH (U:User)-[r:MemberOf|:AdminTo*1..]->(C:Computer)
WITH U.name as n, COUNT(DISTINCT(C)) as c 
WHERE c>1
RETURN n
ORDER BY c DESC

6. Show all users that are administrative on at least one machine, ranked by the number of machines they are admin on.
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH u, COLLECT(c1) + COLLECT(c2) AS tempVar
UNWIND tempVar AS computers
RETURN u.name AS UserName,COUNT(DISTINCT(computers)) AS AdminRightCount
ORDER BY AdminRightCount DESC

7. This will return cross domain 'HasSession' relationships
MATCH p=((S:Computer)-[r:HasSession*1]->(T:User)) 
WHERE NOT S.domain = T.domain
RETURN p
8. Find all other Rights Domain Users shouldn't have
MATCH p=(m:Group)->[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) 
WHERE m.name STARTS WITH ‘DOMAIN USERS’ 
RETURN p


9. Show Kerberoastable high value targets
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
10. Show computers where Domain Admins are logged in
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@EXAMPLE.COM"})
WITH n as DAaccount
MATCH (c:Computer)-[b:MemberOf]->(t:Group) WHERE NOT t.name = "DOMAIN CONTROLLERS@EXAMPLE.COM"
WITH c as NonDC
MATCH p = (NonDC)-[:HasSession]->(DAaccount)
11. Show groups with most localAdmin
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c:Computer)
WITH g,expAdmin,COUNT(DISTINCT(c)) as unrolledAdmin
RETURN g.name,expAdmin,unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin DESC 

or
MATCH (g:Group)
OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH g,COLLECT(c1) + COLLECT(c2) AS tempVar
UNWIND tempVar AS comps
RETURN g.name,COUNT(DISTINCT(comps)) AS compcount
ORDER BY compcount DESC
or
MATCH (g:Group)
MATCH (g)-[:AdminTo]->(c:Computer)
RETURN g.name,COUNT(c)
ORDER BY COUNT(c) DESC
or
MATCH (g:group)-[:AdminTo]->(c:Computer)
RETURN g.name,COUNT(c)
ORDER BY COUNT(c) DESC
or
MATCH (g:Group)
MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c:Computer)
RETURN g.name,COUNT(c)
ORDER BY COUNT(c) DESC
12. List of unique users with a path (no "GetChanges" path) to a Group tagged as "highvalue"
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
13. Return all users which can rdp to any system, if they belong to adm or svr accounts
MATCH (c:Computer) where c.name contains 'xxxxxx'
MATCH (n:User)-[r:MemberOf]->(g:Group)  WHERE g.name = 'DOMAIN ADMINS@EXAMPLE.COM'
optional match (g:Group)-[:CanRDP]->(c)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c) where u1.enabled = true and u1.name contains 'ADM' OR u1.name contains 'SVR'
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c) where u2.enabled = true and u2.name contains 'ADM' OR u2.name contains 'SVR'
WITH COLLECT(u1) + COLLECT(u2) + collect(n) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COLLECT(users.name) as usernames
ORDER BY usernames  desc 
14. Show the number of users that have admin rights on each computer, in descending order
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:AdminTo]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c)
WITH COLLECT(u1) + COLLECT(u2) AS tempVar,c
UNWIND tempVar AS admins
RETURN c.name AS computerName,COUNT(DISTINCT(admins)) AS adminCount
ORDER BY adminCount DESC
15. Stats percentage of enabled users that have a path to a high value group
MATCH (u:User {domain:'EXAMPLE.COM',enabled:True})
MATCH (g:Group {domain:'EXAMPLE.COM'})
WHERE g.highvalue = True
WITH g, COUNT(u) as userCount
MATCH p = shortestPath((u:User {domain:'EXAMPLE.COM',enabled:True})-[*1..]->(g))
RETURN toint(100.0 * COUNT(distinct u) / userCount)
16. Shows machines that allow delegation that arent DCs
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group)  
WHERE NOT t.name =~ "(?i)DOMAIN CONTROLLERS*."  
WITH c as NonDC  
MATCH (NonDC {unconstraineddelegation:true}) RETURN NonDC.name
17. Groups with most local admin
MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
RETURN g.name,expAdmin,expAdmin as totalAdmin
ORDER BY totalAdmin DESC
18. Find principles with DCSync rights
MATCH p=(n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: {result}}) WITH p,n1 
MATCH p2=(n1)-[:MemberOf|GetChangesAll*1..]->(u:Domain {name: {result}}) WITH p,p2 
MATCH p3=(n2)-[:MemberOf|GenericAll|AllExtendedRights*1..]->(u:Domain {name: {result}}) 
RETURN p,p2,p3
19. Find all Windows 7 computers
MATCH (c:Computer)
WHERE toUpper(c.operatingsystem) CONTAINS "Windows 7"
RETURN c
20. Find every user object where the "userpassword" attribute is populated. Neo4j web console only.
MATCH (u:User)
WHERE NOT u.userpassword IS null
RETURN u.name,u.userpassword
21. Find every user that doesn't require kerberos pre-authentication. Neo4j web console only.
MATCH (u:User {dontreqpreauth: true})
RETURN u.name
22. Return any group where the name of the group contains the string "SQL" followed by the string "ADMIN". This will match "SQL ADMINS" and will also match "SQL 2017 ADMINS". Neo4j web console only.
MATCH (g:Group)
WHERE g.name =~ '(?i).*SQL.*ADMIN.*'
RETURN g.name
23. Find every OU that contains the string "CITRIX".
MATCH (o:OU)
WHERE o.name =~ "(?i).*CITRIX.*"
RETURN o
24. Find all computers with sessions from users of a different domain (Looking for cross-domain compromise opportunities). Neo4j web console only.
MATCH (c:Computer)-[:HasSession]->(u:User {domain:'FOO.BAR.COM'})
WHERE NOT c.domain = u.domain
RETURN u.name,COUNT(c)
25. Find all users trusted to perform constrained delegation, return in order of the number of target computers. Neo4j web console only.
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer)
RETURN u.name,COUNT(c)
ORDER BY COUNT(c) DESC
26. Return each OU in the database in order of the number of computers in that OU. Neo4j web console only.
MATCH (o:OU)-[:Contains]->(c:Computer)
RETURN o.name,o.guid,COUNT(c)
ORDER BY COUNT(c) DESC
27. Return the name of every computer in the database where at least one SPN for the computer contains the string "MSSQL". Neo4j web console only.
MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC
28. Find groups with both users and computers that belong to the group. Neo4j web console only.
MATCH (c:Computer)-[r:MemberOf*1..]->(groupsWithComps:Group)
WITH groupsWithComps
MATCH (u:User)-[r:MemberOf*1..]->(groupsWithComps)
RETURN DISTINCT(groupsWithComps) as groupsWithCompsAndUsers
29. Return each OU in the database that contains a Windows Server computer. Return rows where the columns are the name of the OU, the name of the computer, and the operating system of the computer. Neo4j web console only.
MATCH (o:OU)-[:Contains]->(c:Computer)
WHERE toUpper(o.name) STARTS WITH "WINDOWS SERVER"
RETURN o.name,c.name,c.operatingsystem
30. Find any computer that is NOT a domain controller that is trusted to perform unconstrained delegation. Neo4j web console only.
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH COLLECT(c1.name) AS domainControllers
MATCH (c2:Computer {unconstraineddelegation:true})
WHERE NOT c2.name IN domainControllers
RETURN c2.name,c2.operatingsystem
ORDER BY c2.name ASC
31. Find every instance of a computer account having local admin rights on other computers. Return in descending order of the number of computers the computer account has local admin rights on.
MATCH (c1:Computer)
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer)
OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer)
WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1
UNWIND tempVar AS computers
RETURN c1.name,COUNT(DISTINCT(computers))
ORDER BY COUNT(DISTINCT(computers)) DESC
32. Find users who are not marked as "Sensitive and Cannot Be Delegated" that have Administrative access to a computer, and where those users have sessions on servers with Unconstrained Delegation enabled
MATCH (n {sensitive:FALSE})-[:MemberOf*0..5]->(g:Group)-[:AdminTo]->(c:Computer) 
WITH n,c MATCH (d:Computer {unconstraineddelegation:TRUE})-[:HasSession]->(n:User) 
RETURN n.name AS user,
c.name as AdminTo ,
d.name as TicketLocation
----or this one does sort of the same thing?-----
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u,c1
MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u)
RETURN u.name AS user,c1.name AS AdminTo,c2.name AS TicketLocation
ORDER BY user ASC
33. Gather the computers where the user is AdminTo and the computers with unconstrained delegation in lists (for better presentation and structure)
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer)
WITH u,c1
MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u)
RETURN u.name AS user,COLLECT(DISTINCT(c1.name)) AS AdminTo,COLLECT(DISTINCT(c2.name)) AS TicketLocation
ORDER BY user ASC
34. DOMAIN USERS is Admin of Computer
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
35. Find Shortest Path from DOMAIN USERS to High Value Targets
MATCH (g:Group),(n {highvalue:true}),p=shortestPath((g)-[*1..]->(n)) 
WHERE g.name STARTS WITH 'DOMAIN USERS' 
RETURN p
36. Find ALL Path from DOMAIN USERS to High Value Targets
MATCH (g:Group) WHERE g.name STARTS WITH 'DOMAIN USERS'  MATCH (n {highvalue:true}),p=shortestPath((g)-[r*1..]-
37. Find all other right Domain Users shouldn’t have
MATCH p=(m:Group)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) WHERE m.name STARTS WITH 'DOMAIN USERS' RETURN p
38. Kerberoastable Accounts
MATCH (n:User)WHERE n.hasspn=true RETURN n
39. Kerberoastable Accounts member of High Value Group
MATCH (n:User)-[r:MemberOf]->(g:Group) WHERE g.highvalue=true AND n.hasspn=true RETURN n, g, r
40. Find WORKSTATIONS where Domain Users can RDP To
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' AND NOT c.operatingsystem CONTAINS 'Server' 
return p
41. Find SERVERS where Domain Users can RDP To
match p=(g:Group)-[:CanRDP]->(c:Computer) 
where g.name STARTS WITH 'DOMAIN USERS' 
AND c.operatingsystem CONTAINS 'Server' 
return p
42. Top 10 Computers with Most Admins
MATCH (n:User),(m:Computer), (n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m, count(r) as rel_count order by rel_count desc LIMIT 10 MATCH p=(m)<-[r:AdminTo]-(n) RETURN p
43. How many High Value Target a Node can reach
MATCH p = shortestPath((n)-[*1..]->(m {highvalue:true}))
WHERE NOT n = m
RETURN DISTINCT(m.name),LABELS(m)[0],COUNT(DISTINCT(n))
ORDER BY COUNT(DISTINCT(n)) DESC
44. All DA Account Sessions
MATCH (n:User)-[:MemberOf]->(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
MATCH p = (c:Computer)-[:HasSession]->(n) 
return p
45. DA Sessions to NON DC
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) 
WHERE NOT t.name = "DOMAIN CONTROLLERS@TESTLAB.LOCAL"
WITH c as NonDC
MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->
(g:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"})
RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions
ORDER BY COUNT(DISTINCT(NonDC)) DESC
46. Count on how many non DC machines DA have sessions
MATCH (c:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH c.name as DomainControllers
MATCH p = (c2:Computer)-[:HasSession]->(u:User)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-512" AND NOT c2.name in DomainControllers
RETURN DISTINCT(u.name),COUNT(DISTINCT(c2))
ORDER BY COUNT(DISTINCT(c2)) DESC
47. EXCLUDE PATH (Edge) on the fly
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((n)-[r*1..]->(m)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
RETURN p
48. Set SPN to a User
MATCH (n:User {name:"JNOA00093@TESTLAB.LOCAL"}) SET n.hasspn=true
49. Add DOMAIN USERS as Admin to COMPxxxx
MERGE (n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}) WITH n MERGE (m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) WITH n,m MERGE (n)-[:AdminTo]->(m)

50. Test our New Relationship
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
RETURN p
Might try to replace AdminTo by 1**
51. Delete a Relationship
MATCH p=(n:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"})-[r:AdminTo]->
(m:Computer {name:"COMP00673.TESTLAB.LOCAL"}) 
DELETE r
52. Shortest Path from Domain Users to Domain Admins
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
return p
53. Shortest Path from Domain Users to Domain Admins (No AdminTo)
MATCH (g:Group {name:"DOMAIN USERS@TESTLAB.LOCAL"}),
(n {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}),
p=shortestPath((g)-[r*1..]->(n)) 
WHERE ALL(x in relationships(p) WHERE type(x) <> "AdminTo") 
return p
54. Find Admin Group not tagged highvalue
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
RETURN g.name
55. Set Admin Group as highvalue
MATCH (g:Group)
WHERE g.name CONTAINS "ADMIN"
AND g.highvalue IS null
SET g.highvalue=true
56. Remore Admin User highvalue
MATCH p=(g:Group {highvalue: true})<-[:MemberOf]-(u:User)
WHERE g.name CONTAINS "ADMIN"
SET g.highvalue = NULL
57. If you want to enumerate all available properties
Match (n:Computer) return properties(n)
58. List all computers with unconstraineddelegation
MATCH (c:Computer {unconstraineddelegation:true})
return c.name
59. Computer where the most users can RDP to
MATCH (c:Computer)
OPTIONAL MATCH (u1:User)-[:CanRDP]->(c)
OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c)
WITH COLLECT(u1) + COLLECT(u2) as tempVar,c
UNWIND tempVar as users
RETURN c.name,COUNT(DISTINCT(users))
ORDER BY COUNT(DISTINCT(users)) DESC
60. Average Length of Path
MATCH (g:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'})
MATCH p = shortestPath((u:User)-[r:AdminTo|MemberOf|HasSession*1..]->(g))
WITH EXTRACT(n in NODES(p) | LABELS(n)[0]) as pathNodes
WITH FILTER(x IN pathNodes WHERE x = "Computer") as filteredPathNodes
RETURN AVG(LENGTH(filteredPathNodes))
61. Count how many Users have a path to DA
MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
RETURN COUNT (DISTINCT(u))
62. Percent of Users that have a path to DA
OPTIONAL MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@TESTLAB.LOCAL"}))
OPTIONAL MATCH (uT:User)
WITH COUNT (DISTINCT(uT)) as uTotal, COUNT (DISTINCT(u)) as uHasPath
RETURN uHasPath / uTotal * 100 as Percent
63. Return users with shortest paths to high value targets by name
MATCH (u:User)
MATCH (g:Group {highvalue: True})
MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner*1..]->(g))
RETURN DISTINCT(u.name),u.enabled
order by u.name
64. Return labels i.e. group,username for a given SID
MATCH (n {objectsid:"S-1-5-21-971234526-3761234589-3049876599-500"}) RETURN labels(n)
65. Return top 10 users with most Derivative local admin rights
MATCH (u:User)
OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer)
OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer)
WITH COLLECT(c1) + COLLECT(c2) as tempVar,u
UNWIND tempVar AS computers
RETURN u.name,COUNT(DISTINCT(computers)) AS is_admin_on_this_many_boxes
ORDER BY is_admin_on_this_many_boxes DESC
66. Find all users WITHOUT a path to da
MATCH (n:User),(m:Group {name:"DOMAIN ADMINS@CONTOSO.LOCAL"}) WHERE NOT EXISTS ((n)-[*]->(m)) RETURN n.name 
67. Find users that have never logged on AND their account is still active
MATCH (n:User) WHERE n.lastlogontimestamp=-1.0 AND n.enabled=TRUE RETURN n.name ORDER BY n.name  
68. Find users with DCSync rights who are not members of DA
MATCH (n1)-[:MemberOf|GetChanges*1..]->(u:Domain {name: "TESTLAB.LOCAL"}) WITH n1,u 
MATCH (n1)-[:MemberOf|GetChangesAll*1..]->(u) WITH n1,u 
MATCH p = (n1)-[:MemberOf|GetChanges|GetChangesAll*1..]->(u) WHERE NOT (n1)-[:MemberOf*1..]->(:Group {name:"DOMAIN ADMINS@TESTLAB.LOCAL"}) 
RETURN p
69. Show attack paths from X domain to Specific Domain
MATCH p=(n)-[r]->(m) WHERE NOT n.domain = m.domain RETURN p  
or specific domains
MATCH p=(n {domain:"lab.local")-[r]->(m {domain:"test.local"}) RETURN p
70. Find all computers that are NOT dc's
MATCH (dc:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.name STARTS WITH('DOMAIN CONTROLLERS')
WITH COLLECT(dc) as dcs
MATCH (c:Computer) WHERE NOT c in dcs
RETURN COUNT(c)
or using objectID
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectid ENDS WITH "-516"
WITH COLLECT(c1) as dcs
MATCH (c:Computer) WHERE NOT c in dcs
RETURN COUNT(c)
References
Reading materials / References::
https://github.com/BloodHoundAD/BloodHound/wiki
https://github.com/Scoubi/BloodhoundAD-Queries
https://github.com/porterhau5/BloodHound-Owned
https://porterhau5.com/blog/extending-bloodhound-track-and-visualize-your-compromise/
https://blog.cptjesus.com/posts/introtocypher#building-on-top
https://github.com/awsmhacks/awsmBloodhoundCustomQueries#readme> 


To also make life easier, I’ve taken the applicable queries here and made them compatible within the “Custom Queries” section in the GUI. You can download that here: https://github.com/hausec/Bloodhound-Custom-Queries

Table of Contents
GUI/Graph Queries

Console Queries

Find All edges any owned user has on a computer
MATCH p=shortestPath((m:User)-[r]->(b:Computer)) WHERE m.owned RETURN p

Find All Users with an SPN/Find all Kerberoastable Users            
MATCH (n:User)WHERE n.hasspn=true RETURN n

Find All Users with an SPN/Find all Kerberoastable Users with passwords last set > 5 years ago       
MATCH (u:User) WHERE u.hasspn=true AND u.pwdlastset < (datetime().epochseconds - (1825 * 86400)) AND NOT u.pwdlastset IN [-1.0, 0.0]
RETURN u.name, u.pwdlastset order by u.pwdlastset

Find SPNs with keywords (swap SQL with whatever)      
MATCH (u:User) WHERE ANY (x IN u.serviceprincipalnames WHERE toUpper(x) CONTAINS 'SQL')RETURN u

Kerberoastable Users with a path to DA              
MATCH (u:User {hasspn:true}) MATCH (g:Group) WHERE g.name CONTAINS 'DOMAIN ADMINS' MATCH p = shortestPath( (u)-[*1..]->(g) ) RETURN p

Find workstations a user can RDP into. 
match p=(g:Group)-[:CanRDP]->(c:Computer) where g.objectid ENDS WITH '-513'  AND NOT c.operatingsystem CONTAINS 'Server' return p

Find servers a user can RDP into.            
match p=(g:Group)-[:CanRDP]->(c:Computer) where  g.objectid ENDS WITH '-513'  AND c.operatingsystem CONTAINS 'Server' return p   

DA sessions not on a certain group (e.g. domain controllers)
OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) WHERE NOT t.name = 'DOMAIN CONTROLLERS@TESTLAB.LOCAL' WITH c as NonDC MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group {name:”DOMAIN ADMINS@TESTLAB.LOCAL”}) RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions ORDER BY COUNT(DISTINCT(NonDC)) DESC

Find all computers with Unconstrained Delegation         
MATCH (c:Computer {unconstraineddelegation:true}) return c

Find unsupported OSs  
MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2008|xp|vista|7|me)*.' RETURN H
 

Find users that logged in within the last 90 days. Change 90 to whatever threshold you want.         
MATCH (u:User) WHERE u.lastlogon < (datetime().epochseconds - (90 * 86400)) and NOT u.lastlogon IN [-1.0, 0.0] RETURN u

Find users with passwords last set thin the last 90 days. Change 90 to whatever threshold you want.
MATCH (u:User) WHERE u.pwdlastset < (datetime().epochseconds - (90 * 86400)) and NOT u.pwdlastset IN [-1.0, 0.0] RETURN u

Find all sessions any user in a specific domain has
MATCH p=(m:Computer)-[r:HasSession]->(n:User {domain: "TEST.LOCAL"}) RETURN p

View all GPOs   
Match (n:GPO) return n

View all GPOs that contain a keyword   
Match (n:GPO) WHERE n.name CONTAINS "SERVER" return n

View all groups that contain the word ‘admin’  
Match (n:Group) WHERE n.name CONTAINS "ADMIN" return n

Find user that doesn’t require kerberos pre-authentication (aka AS-REP Roasting)          
MATCH (u:User {dontreqpreauth: true}) RETURN u

Find a group with keywords. E.g. SQL ADMINS or SQL 2017 ADMINS      
MATCH (g:Group) WHERE g.name =~ '(?i).SQL.ADMIN.*' RETURN g

Show all high value target group             
MATCH p=(n:User)-[r:MemberOf*1..]->(m:Group {highvalue:true}) RETURN p

Shortest paths to Domain Admins group from computers:          
MATCH (n:Computer),(m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p

Shortest paths to Domain Admins group from computers excluding potential DCs (based on ldap/ and GC/ spns):              
WITH '(?i)ldap/.*' as regex_one WITH '(?i)gc/.*' as regex_two MATCH (n:Computer) WHERE NOT ANY(item IN n.serviceprincipalnames WHERE item =~ regex_two OR item =~ regex_two ) MATCH(m:Group {name:"DOMAIN ADMINS@DOMAIN.GR"}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p

Shortest paths to Domain Admins group from all domain groups (fix-it):              
MATCH (n:Group),(m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p

Shortest paths to Domain Admins group from non-privileged groups (AdminCount=false)              
MATCH (n:Group {admincount:false}),(m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p

Shortest paths to Domain Admins group from the Domain Users group:              
MATCH (g:Group) WHERE g.name =~ 'DOMAIN USERS@.*' MATCH (g1:Group) WHERE g1.name =~ 'DOMAIN ADMINS@.*' OPTIONAL MATCH p=shortestPath((g)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(g1)) RETURN p

Find interesting privileges/ACEs that have been configured to DOMAIN USERS group:   
MATCH (m:Group) WHERE m.name =~ 'DOMAIN USERS@.*' MATCH p=(m)-[r:Owns|:WriteDacl|:GenericAll|:WriteOwner|:ExecuteDCOM|:GenericWrite|:AllowedToDelegate|:ForceChangePassword]->(n:Computer) RETURN p

Shortest paths to Domain Admins group from non privileged users (AdminCount=false):              
MATCH (n:User {admincount:false}),(m:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p

Find all Edges that a specific user has against all the nodes (HasSession is not calculated, as it is an edge that comes from computer to user, not from user to computer):    
MATCH (n:User) WHERE n.name =~ 'HELPDESK@DOMAIN.GR'MATCH (m) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(m)) RETURN p

Find all the Edges that any UNPRIVILEGED user (based on the admincount:False) has against all the nodes:    
MATCH (n:User {admincount:False}) MATCH (m) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(m)) RETURN p

Find interesting edges related to “ACL Abuse” that uprivileged users have against other users:   
MATCH (n:User {admincount:False}) MATCH (m:User) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:AllExtendedRights|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner*1..]->(m)) RETURN p

Find interesting edges related to “ACL Abuse” that unprivileged users have against computers:        
MATCH (n:User {admincount:False}) MATCH p=allShortestPaths((n)-[r:AllExtendedRights|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|AdminTo|CanRDP|ExecuteDCOM|ForceChangePassword*1..]->(m:Computer)) RETURN p

Find if unprivileged users have rights to add members into groups:        
MATCH (n:User {admincount:False}) MATCH p=allShortestPaths((n)-[r:AddMember*1..]->(m:Group)) RETURN p

Find the active user sessions on all domain computers: 
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(c:Computer)-[*1]->(u1) RETURN p2

Find all the privileges (edges) of the domain users against the domain computers (e.g. CanRDP, AdminTo etc. HasSession edge is not included):            
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[*1]->(c:Computer) RETURN p2

Find only the AdminTo privileges (edges) of the domain users against the domain computers:        
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[:AdminTo*1..]->(c:Computer) RETURN p2

Find only the CanRDP privileges (edges) of the domain users against the domain computers:        
MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[:CanRDP*1..]->(c:Computer) RETURN p2

Display in BH a specific user with constrained deleg and his targets where he allowed to delegate:            
MATCH (u:User {name:'USER@DOMAIN.GR'}),(c:Computer),p=((u)-[r:AllowedToDelegate]->(c)) RETURN p

Console Queries

Find what groups can RDP          
MATCH p=(m:Group)-[r:CanRDP]->(n:Computer) RETURN m.name, n.name ORDER BY m.name

Find what groups can reset passwords  
MATCH p=(m:Group)-[r:ForceChangePassword]->(n:User) RETURN m.name, n.name ORDER BY m.name

Find what groups have local admin rights           
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) RETURN m.name, n.name ORDER BY m.name

Find what users have local admin rights              
MATCH p=(m:User)-[r:AdminTo]->(n:Computer) RETURN m.name, n.name ORDER BY m.name

List the groups of all owned users           
MATCH (m:User) WHERE m.owned=TRUE WITH m MATCH p=(m)-[:MemberOf*1..]->(n:Group) RETURN m.name, n.name ORDER BY m.name

List the unique groups of all owned users           
MATCH (m:User) WHERE m.owned=TRUE WITH m MATCH (m)-[r:MemberOf*1..]->(n:Group) RETURN DISTINCT(n.name)

All active DA sessions    
MATCH (n:User)-[:MemberOf*1..]->(g:Group) WHERE g.objectid ENDS WITH '-512' MATCH p = (c:Computer)-[:HasSession]->(n) return p

Find all active sessions a member of a group has             
MATCH (n:User)-[:MemberOf*1..]->(g:Group {name:'DOMAIN ADMINS@TESTLAB.LOCAL'}) MATCH p = (c:Computer)-[:HasSession]->(n) return p

Can an object from domain ‘A’ do anything to an object in domain ‘B’   
MATCH (n {domain:"TEST.LOCAL"})-[r]->(m {domain:"LAB.LOCAL"}) RETURN LABELS(n)[0],n.name,TYPE(r),LABELS(m)[0],m.name

Find all connections to a different domain/forest            
MATCH (n)-[r]->(m) WHERE NOT n.domain = m.domain RETURN LABELS(n)[0],n.name,TYPE(r),LABELS(m)[0],m.name

Find All Users with an SPN/Find all Kerberoastable Users with passwords last set > 5 years ago (In Console)              
MATCH (u:User) WHERE n.hasspn=true AND WHERE u.pwdlastset < (datetime().epochseconds - (1825 * 86400)) and NOT u.pwdlastset IN [-1.0, 0.0] RETURN u.name, u.pwdlastset order by u.pwdlastset

Kerberoastable Users with most privileges         

MATCH (u:User {hasspn:true}) OPTIONAL MATCH (u)-[:AdminTo]->(c1:Computer) OPTIONAL MATCH (u)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) WITH u,COLLECT(c1) + COLLECT(c2) AS tempVar UNWIND tempVar AS comps RETURN u.name,COUNT(DISTINCT(comps)) ORDER BY COUNT(DISTINCT(comps)) DESC

Find users that logged in within the last 90 days. Change 90 to whatever threshold you want. (In Console)         
MATCH (u:User) WHERE u.lastlogon < (datetime().epochseconds - (90 * 86400)) and NOT u.lastlogon IN [-1.0, 0.0] RETURN u.name, u.lastlogon order by u.lastlogon

Find users with passwords last set within the last 90 days. Change 90 to whatever threshold you want. (In Console)            
MATCH (u:User) WHERE u.pwdlastset < (datetime().epochseconds - (90 * 86400)) and NOT u.pwdlastset IN [-1.0, 0.0] RETURN u.name, u.pwdlastset order by u.pwdlastset

List users and their login times + pwd last set times in human readable format
MATCH (n:User) WHERE n.enabled = TRUE RETURN n.name, datetime({epochSeconds: toInteger(n.pwdlastset) }), datetime({epochSeconds: toInteger(n.lastlogon) }) order by n.pwdlastset

Find constrained delegation (In Console)            
MATCH (u:User)-[:AllowedToDelegate]->(c:Computer) RETURN u.name,COUNT(c) ORDER BY COUNT(c) DESC

View OUs based on member count. (In Console)             
MATCH (o:OU)-[:Contains]->(c:Computer) RETURN o.name,o.guid,COUNT(c) ORDER BY COUNT(c) DESC

Return each OU that has a Windows Server in it (In Console)     
MATCH (o:OU)-[:Contains]->(c:Computer) WHERE toUpper(c.operatingsystem) STARTS WITH "WINDOWS SERVER" RETURN o.name

Find computers that allow unconstrained delegation that AREN’T domain controllers. (In Console)             
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group) WHERE g.objectsid ENDS WITH '-516' WITH COLLECT(c1.name) AS domainControllers MATCH (c2:Computer {unconstraineddelegation:true}) WHERE NOT c2.name IN domainControllers RETURN c2.name,c2.operatingsystem ORDER BY c2.name ASC

Find the number of principals with control of a “high value” asset where the principal itself does not belong to a “high value” group             
MATCH (n {highvalue:true}) OPTIONAL MATCH (m1)-[{isacl:true}]->(n) WHERE NOT (m1)-[:MemberOf*1..]->(:Group {highvalue:true}) OPTIONAL MATCH (m2)-[:MemberOf*1..]->(:Group)-[{isacl:true}]->(n) WHERE NOT (m2)-[:MemberOf*1..]->(:Group {highvalue:true}) WITH n,COLLECT(m1) + COLLECT(m2) AS tempVar UNWIND tempVar AS controllers RETURN n.name,COUNT(DISTINCT(controllers)) ORDER BY COUNT(DISTINCT(controllers)) DESC

Enumerate all properties (In Console)   
Match (n:Computer) return properties(n)
Match users that are not AdminCount 1, have generic all, and no local admin   
MATCH (u:User)-[:GenericAll]->(c:Computer) WHERE  NOT u.admincount AND NOT (u)-[:AdminTo]->(c) RETURN u.name, c.name

What permissions does Everyone/Authenticated users/Domain users/Domain computers have
MATCH p=(m:Group)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChanges|GetChangesAll|HasSession|Owns|ReadLAPSPassword|SQLAdmin|TrustedBy|WriteDACL|WriteOwner|AddAllowedToAct|AllowedToAct]->(t) WHERE m.objectsid ENDS WITH '-513' OR m.objectsid ENDS WITH '-515' OR m.objectsid ENDS WITH 'S-1-5-11' OR m.objectsid ENDS WITH 'S-1-1-0' RETURN m.name,TYPE(r),t.name,t.enabled

Find computers with descriptions and display them (along with the description, sometimes admins save sensitive data on domain objects descriptions like passwords):      
MATCH (c:Computer) WHERE c.description IS NOT NULL RETURN c.name,c.description

Return the name of every computer in the database where at least one SPN for the computer contains the string “MSSQL”:
MATCH (c:Computer) WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS 'MSSQL') RETURN c.name,c.serviceprincipalnames ORDER BY c.name ASC

Find any computer that is NOT a domain controller and it is trusted to perform unconstrained delegation:         
MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group) WHERE g.objectid ENDS WITH '-516' WITH COLLECT(c1.name) AS domainControllers MATCH (c2:Computer {unconstraineddelegation:true}) WHERE NOT c2.name IN domainControllers RETURN c2.name,c2.operatingsystem ORDER BY c2.name ASC

Find every computer account that has local admin rights on other computers. Return in descending order of the number of computers the computer account has local admin rights to:            
MATCH (c1:Computer) OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer) OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer) WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1 UNWIND tempVar AS computers RETURN c1.name AS COMPUTER,COUNT(DISTINCT(computers)) AS ADMIN_TO_COMPUTERS ORDER BY COUNT(DISTINCT(computers)) DESC

Alternatively, find every computer that has local admin rights on other computers and display these computers:            
MATCH (c1:Computer) OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer) OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer) WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1 UNWIND tempVar AS computers RETURN c1.name AS COMPUTER,COLLECT(DISTINCT(computers.name)) AS ADMIN_TO_COMPUTERS ORDER BY c1.name

Get the names of the computers without admins, sorted by alphabetic order:   
MATCH (n)-[r:AdminTo]->(c:Computer) WITH COLLECT(c.name) as compsWithAdmins MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins RETURN c2.name ORDER BY c2.name ASC

Show computers (excluding Domain Controllers) where Domain Admins are logged in: 
MATCH (n:User)-[:MemberOf*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'}) WITH n as privusers

Find the percentage of computers with path to Domain Admins:            
MATCH (totalComputers:Computer {domain:'DOMAIN.GR'}) MATCH p=shortestPath((ComputersWithPath:Computer {domain:'DOMAIN.GR'})-[r*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'})) WITH COUNT(DISTINCT(totalComputers)) as totalComputers, COUNT(DISTINCT(ComputersWithPath)) as ComputersWithPath RETURN 100.0 * ComputersWithPath / totalComputers AS percentComputersToDA

Find on each computer who can RDP (searching only enabled users):     
MATCH (c:Computer) OPTIONAL MATCH (u:User)-[:CanRDP]->(c) WHERE u.enabled=true OPTIONAL MATCH (u1:User)-[:MemberOf*1..]->(:Group)-[:CanRDP]->(c) where u1.enabled=true WITH COLLECT(u) + COLLECT(u1) as tempVar,c UNWIND tempVar as users RETURN c.name AS COMPUTER,COLLECT(DISTINCT(users.name)) as USERS ORDER BY USERS desc

Find on each computer the number of users with admin rights (local admins) and display the users with admin rights:      
MATCH (c:Computer) OPTIONAL MATCH (u1:User)-[:AdminTo]->(c) OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c) WITH COLLECT(u1) + COLLECT(u2) AS TempVar,c UNWIND TempVar AS Admins RETURN c.name AS COMPUTER, COUNT(DISTINCT(Admins)) AS ADMIN_COUNT,COLLECT(DISTINCT(Admins.name)) AS USERS ORDER BY ADMIN_COUNT DESC
Active Directory group with default privileged rights on domain users and groups, plus the ability to logon to Domain Controllers        

MATCH (u:User)-[r1:MemberOf*1..]->(g1:Group {name:'ACCOUNT OPERATORS@DOMAIN.GR'}) RETURN u.name
Find which domain Groups are Admins to what computers:       

MATCH (g:Group) OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar UNWIND tempVar AS computers RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights

Find which domain Groups (excluding the Domain Admins and Enterprise Admins) are Admins to what computers:      
MATCH (g:Group) WHERE NOT (g.name =~ '(?i)domain admins@.*' OR g.name =~ "(?i)enterprise admins@.*") OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar UNWIND tempVar AS computers RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights

Find which domain Groups (excluding the high privileged groups marked with AdminCount=true) are Admins to what computers:       
MATCH (g:Group) WHERE g.admincount=false OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar UNWIND tempVar AS computers RETURN g.name AS GROUP, COLLECT(computers.name) AS AdminRights

Find the most privileged groups on the domain (groups that are Admins to Computers. Nested groups will be calculated):          

MATCH (g:Group) OPTIONAL MATCH (g)-[:AdminTo]->(c1:Computer) OPTIONAL MATCH (g)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c2:Computer) WITH g, COLLECT(c1) + COLLECT(c2) AS tempVar UNWIND tempVar AS computers RETURN g.name AS GroupName,COUNT(DISTINCT(computers)) AS AdminRightCount ORDER BY AdminRightCount DESC
Find the number of computers that do not have local Admins:  

MATCH (n)-[r:AdminTo]->(c:Computer) WITH COLLECT(c.name) as compsWithAdmins MATCH (c2:Computer) WHERE NOT c2.name in compsWithAdmins RETURN COUNT(c2)
Find groups with most local admins (either explicit admins or derivative/unrolled):        

MATCH (g:Group) WITH g OPTIONAL MATCH (g)-[r:AdminTo]->(c1:Computer) WITH g,COUNT(c1) as explicitAdmins OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c2:Computer) WITH g,explicitAdmins,COUNT(DISTINCT(c2)) as unrolledAdmins RETURN g.name,explicitAdmins,unrolledAdmins, explicitAdmins + unrolledAdmins as totalAdmins ORDER BY totalAdmins DESC
Find percentage of non-privileged groups (based on admincount:false) to Domain Admins group:  

MATCH (totalGroups:Group {admincount:false}) MATCH p=shortestPath((GroupsWithPath:Group {admincount:false})-[r*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'})) WITH COUNT(DISTINCT(totalGroups)) as totalGroups, COUNT(DISTINCT(GroupsWithPath)) as GroupsWithPath RETURN 100.0 * GroupsWithPath / totalGroups AS percentGroupsToDA
Find every user object where the “userpassword” attribute is populated (wald0):           

MATCH (u:User) WHERE NOT u.userpassword IS null RETURN u.name,u.userpassword
Find every user that doesn’t require kerberos pre-authentication (wald0):          

MATCH (u:User {dontreqpreauth: true}) RETURN u.name
Find all users trusted to perform constrained delegation. The result is ordered by the amount of computers:  

MATCH (u:User)-[:AllowedToDelegate]->(c:Computer) RETURN u.name,COUNT(c) ORDER BY COUNT(c) DESC
Find the active sessions that a specific domain user has on all domain computers:          

MATCH p1=shortestPath(((u1:User {name:'USER@DOMAIN.GR'})-[r1:MemberOf*1..]->(g1:Group))) MATCH (c:Computer)-[r:HasSession*1..]->(u1) RETURN DISTINCT(u1.name) as users, c.name as computers ORDER BY computers

Count the number of the computers where each domain user has direct Admin privileges to:         
MATCH (u:User)-[:AdminTo]->(c:Computer) RETURN count(DISTINCT(c.name)) AS COMPUTER, u.name AS USER ORDER BY count(DISTINCT(c.name)) DESC

Count the number of the computers where each domain user has derivative Admin privileges to:     
MATCH (u:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c:Computer) RETURN count(DISTINCT(c.name)) AS COMPUTER, u.name AS USER ORDER BY u.name

Display the computer names where each domain user has derivative Admin privileges to:              
MATCH (u:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c:Computer) RETURN DISTINCT(c.name) AS COMPUTER, u.name AS USER ORDER BY u.name

Find Kerberoastable users who are members of high value groups:        
MATCH (u:User)-[r:MemberOf*1..]->(g:Group) WHERE g.highvalue=true AND u.hasspn=true RETURN u.name AS USER

Find Kerberoastable users and where they are AdminTo:            
OPTIONAL MATCH (u1:User) WHERE u1.hasspn=true OPTIONAL MATCH (u1)-[r:AdminTo]->(c:Computer) RETURN u1.name AS user_with_spn,c.name AS local_admin_to

Find the percentage of users with a path to Domain Admins:     
MATCH (totalUsers:User {domain:'DOMAIN.GR'}) MATCH p=shortestPath((UsersWithPath:User {domain:'DOMAIN.GR'})-[r*1..]->(g:Group {name:'DOMAIN ADMINS@DOMAIN.GR'})) WITH COUNT(DISTINCT(totalUsers)) as totalUsers, COUNT(DISTINCT(UsersWithPath)) as UsersWithPath RETURN 100.0 * UsersWithPath / totalUsers AS percentUsersToDA

Find the percentage of enabled users that have a path to high value groups:     
MATCH (u:User {domain:'DOMAIN.GR',enabled:True}) MATCH (g:Group {domain:'DOMAIN.GR'}) WHERE g.highvalue = True WITH g, COUNT(u) as userCount MATCH p = shortestPath((u:User {domain:'DOMAIN.GR',enabled:True})-[*1..]->(g)) RETURN 100.0 * COUNT(distinct u) / userCount

List of unique users with a path to a Group tagged as “highvalue”:         
MATCH (u:User) MATCH (g:Group {highvalue:true}) MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|TrustedBy|WriteDacl|WriteOwner|GetChanges|GetChangesAll*1..]->(g)) RETURN DISTINCT(u.name) AS USER, u.enabled as ENABLED,count(p) as PATHS order by u.name

Find users who are NOT marked as “Sensitive and Cannot Be Delegated” and have Administrative access to a computer, and where those users have sessions on servers with Unconstrained Delegation enabled (by NotMedic):        
MATCH (u:User {sensitive:false})-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c1:Computer) WITH u,c1 MATCH (c2:Computer {unconstraineddelegation:true})-[:HasSession]->(u) RETURN u.name AS user,COLLECT(DISTINCT(c1.name)) AS AdminTo,COLLECT(DISTINCT(c2.name)) AS TicketLocation ORDER BY user ASC

Find users with constrained delegation permissions and the corresponding targets where they allowed to delegate:           
MATCH (u:User) WHERE u.allowedtodelegate IS NOT NULL RETURN u.name,u.allowedtodelegate

Alternatively, search for users with constrained delegation permissions,the corresponding targets where they are allowed to delegate, the privileged users that can be impersonated (based on sensitive:false and admincount:true) and find where these users (with constrained deleg privs) have active sessions (user hunting) as well as count the shortest paths to them: 

OPTIONAL MATCH (u:User {sensitive:false, admincount:true}) WITH u.name AS POSSIBLE_TARGETS OPTIONAL MATCH (n:User) WHERE n.allowedtodelegate IS NOT NULL WITH n AS USER_WITH_DELEG, n.allowedtodelegate as DELEGATE_TO, POSSIBLE_TARGETS OPTIONAL MATCH (c:Computer)-[:HasSession]->(USER_WITH_DELEG) WITH USER_WITH_DELEG,DELEGATE_TO,POSSIBLE_TARGETS,c.name AS USER_WITH_DELEG_HAS_SESSION_TO OPTIONAL MATCH p=shortestPath((o)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(USER_WITH_DELEG)) WHERE NOT o=USER_WITH_DELEG WITH USER_WITH_DELEG,DELEGATE_TO,POSSIBLE_TARGETS,USER_WITH_DELEG_HAS_SESSION_TO,p RETURN USER_WITH_DELEG.name AS USER_WITH_DELEG, DELEGATE_TO, COLLECT(DISTINCT(USER_WITH_DELEG_HAS_SESSION_TO)) AS USER_WITH_DELEG_HAS_SESSION_TO, COLLECT(DISTINCT(POSSIBLE_TARGETS)) AS PRIVILEGED_USERS_TO_IMPERSONATE, COUNT(DISTINCT(p)) AS PATHS_TO_USER_WITH_DELEG

Find computers with constrained delegation permissions and the corresponding targets where they allowed to delegate:             
MATCH (c:Computer) WHERE c.allowedtodelegate IS NOT NULL RETURN c.name,c.allowedtodelegate

Alternatively, search for computers with constrained delegation permissions, the corresponding targets where they are allowed to delegate, the privileged users that can be impersonated (based on sensitive:false and admincount:true) and find who is LocalAdmin on these computers as well as count the shortest paths to them:            
OPTIONAL MATCH (u:User {sensitive:false, admincount:true}) WITH u.name AS POSSIBLE_TARGETS OPTIONAL MATCH (n:Computer) WHERE n.allowedtodelegate IS NOT NULL WITH n AS COMPUTERS_WITH_DELEG, n.allowedtodelegate as DELEGATE_TO, POSSIBLE_TARGETS OPTIONAL MATCH (u1:User)-[:AdminTo]->(COMPUTERS_WITH_DELEG) WITH u1 AS DIRECT_ADMINS,POSSIBLE_TARGETS,COMPUTERS_WITH_DELEG,DELEGATE_TO OPTIONAL MATCH (u2:User)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(COMPUTERS_WITH_DELEG) WITH COLLECT(DIRECT_ADMINS) + COLLECT(u2) AS TempVar,COMPUTERS_WITH_DELEG,DELEGATE_TO,POSSIBLE_TARGETS UNWIND TempVar AS LOCAL_ADMINS OPTIONAL MATCH p=shortestPath((o)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(COMPUTERS_WITH_DELEG)) WHERE NOT o=COMPUTERS_WITH_DELEG WITH COMPUTERS_WITH_DELEG,DELEGATE_TO,POSSIBLE_TARGETS,p,LOCAL_ADMINS RETURN COMPUTERS_WITH_DELEG.name AS COMPUTERS_WITH_DELG, LOCAL_ADMINS.name AS LOCAL_ADMINS_TO_COMPUTERS_WITH_DELG, DELEGATE_TO, COLLECT(DISTINCT(POSSIBLE_TARGETS)) AS PRIVILEGED_USERS_TO_IMPERSONATE, COUNT(DISTINCT(p)) AS PATHS_TO_USER_WITH_DELEG

Find if any domain user has interesting permissions against a GPO:        
MATCH p=(u:User)-[r:AllExtendedRights|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|GpLink*1..]->(g:GPO) RETURN p LIMIT 25

Show me all the sessions from the users in the OU with the following GUID       
MATCH p=(o:OU {guid:'045939B4-3FA8-4735-YU15-7D61CFOU6500'})-[r:Contains*1..]->(u:User) MATCH (c:Computer)-[rel:HasSession]->(u) return u.name,c.name

Creating a property on the users that have an actual path to anything high_value(heavy query. takes hours on a large dataset)  
MATCH (u:User) MATCH (g:Group {highvalue: true}) MATCH p = shortestPath((u:User)-[r:AddMember|AdminTo|AllExtendedRights|AllowedToDelegate|CanRDP|Contains|ExecuteDCOM|ForceChangePassword|GenericAll|GenericWrite|GetChangesAll|GpLink|HasSession|MemberOf|Owns|ReadLAPSPassword|SQLAdmin|TrustedBy|WriteDacl|WriteOwner|AddAllowedToAct|AllowedToAct*1..]->(g)) SET u.has_path_to_da =true

What “user with a path to any high_value group” has most sessions?    
MATCH (c:Computer)-[rel:HasSession]->(u:User {has_path_to_da: true}) WITH COLLECT(c) as tempVar,u UNWIND tempVar as sessions WITH u,COUNT(DISTINCT(sessions)) as sessionCount RETURN u.name,u.displayname,sessionCount ORDER BY sessionCount desc

Creating a property for Tier 1 Users with regex:
MATCH (u:User) WHERE u.name =~ 'internal_naming_convention[0-9]{2,5}@EXAMPLE.LOCAL' SET u.tier_1_user = true

Creating a property for Tier 1 Computers via group-membership:           
MATCH (c:Computer)-[r:MemberOf*1..]-(g:Group {name:'ALL_SERVERS@EXAMPLE.LOCAL'}) SET c.tier_1_computer = true

Creating a property for Tier 2 Users via group name and an exclusion:   
MATCH (u:User)-[r:MemberOf*1..]-(g:Group)WHERE g.name CONTAINS  'ALL EMPLOYEES' AND NOT u.name contains 'TEST' SET u.tier_2_user = true

Creating a property for Tier 2 Computers via nested groups name:         
MATCH (c:Computer)-[r:MemberOf*1..]-(g:Group) WHERE g.name STARTS WITH 'CLIENT_' SET c.tier_2_computer = true
List Tier2 access to Tier 1 Computers     

 
List Tier 1 Sessions on Tier 2 Computers

MATCH (c:Computer)-[rel:HasSession]->(u:User) WHERE u.tier_1_user = true AND c.tier_2_computer = true RETURN u.name,u.displayname,TYPE(rel),c.name,labels(c),c.enabled

List all users with local admin and count how many instances    
OPTIONAL MATCH (c1)-[:AdminTo]->(c2:Computer) OPTIONAL MATCH (c1)-[:MemberOf*1..]->(:Group)-[:AdminTo]->(c3:Computer) WITH COLLECT(c2) + COLLECT(c3) AS tempVar,c1 UNWIND tempVar AS computers RETURN c1.name,COUNT(DISTINCT(computers)) ORDER BY COUNT(DISTINCT(computers)) DESC

Find all users a part of the VPN group   
Match (u:User)-[:MemberOf]->(g:Group) WHERE g.name CONTAINS "VPN" return u.name,g.name

Find users that have never logged on and account is still active 
MATCH (n:User) WHERE n.lastlogontimestamp=-1.0 AND n.enabled=TRUE RETURN n.name ORDER BY n.name

Adjust Query to Local Timezone (Change timezone parameter)
MATCH (u:User) WHERE NOT u.lastlogon IN [-1.0, 0.0] return u.name, datetime({epochSeconds:toInteger(u.lastlogon), timezone: '+10:00'}) as LastLogon


# BH Observation Queries
```
//1. Targetable users (have to remove the ladmin as they are using cyberark tool)
```
//1. Targetable users
MATCH p=shortestPath((u:User)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@GUWW.NET"}))
RETURN DISTINCT(u.name) as Name,(u.enabled) as Status ,(u.displayname)as DisplayName,(u.email) as Email,(u.title) As Designation ORDER BY Designation,Email

MATCH p=shortestPath((u:User)-[:HasSession*1..]-> (m:Group {name: "DOMAIN ADMINS@GUWW.NET"})) RETURN p


//2. Targetable Computers
MATCH p=shortestPath((c:Computer)-[*1..]->
(m:Group {name: "DOMAIN ADMINS@GUWW.NET"})) where c.enabled=true
RETURN DISTINCT (c.name) as Computer_name,(c.enabled) as Status,(c.operatingsystem) as Operatingsystem, (c.serviceprincipalnames) as SPN

//3. Path to Domain Admin 4 to 5 scenario's has to include 

//4. EOL EOS - LEGACY OSs
MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' and H.enabled = true RETURN H.name AS Computer_Name,H.operatingsystem AS Operatingsyste ,H.serviceprincipalnames AS ServicesRunning

MATCH (H:Computer)-[:HasSession]->(y) WHERE H.operatingsystem =~ '(?i).*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN H.name AS Computer_Name,H.operatingsystem AS Operatingsyste ,H.serviceprincipalnames AS ServicesRunning,

MATCH (H:Computer)-[:HasSession]->(u1:User) WHERE H.operatingsystem =~ '(?i).*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN H.name AS Computer_Name,H.operatingsystem AS Operatingsyste,u1.name ,H.serviceprincipalnames AS ServicesRunning


//5. Found too many Admins with High Privileges

User Level:

MATCH (u1:User)
WITH u1
OPTIONAL MATCH (u1)-[r:AdminTo]->(c:Computer)
WITH u1,COUNT(c) as expAdmin
OPTIONAL MATCH (u1)-[r:MemberOf*1..]->(a:User)-[r2:AdminTo]->(c:Computer)
WITH u1,expAdmin,COUNT(DISTINCT(c)) as unrolledAdmin
RETURN u1.name as User_Name,expAdmin,unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin DESC

MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN u1.name as Username,type(r) as ACL_Permission,n.name as Computer_Name


Group Level:

MATCH (g:Group)
WITH g
OPTIONAL MATCH (g)-[r:AdminTo]->(c:Computer)
WITH g,COUNT(c) as expAdmin
OPTIONAL MATCH (g)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c:Computer)
WITH g,expAdmin,COUNT(DISTINCT(c)) as unrolledAdmin
RETURN g.name as Group_Name,expAdmin,unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin DESC

MATCH p=(m:Group)-[r:AdminTo]->(m1:Group) RETURN m.name as Group_Name,type(r) as ACL Permission,m1.name as Group_Name

MATCH p = (g1:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2:AdminTo]->(n:Computer) RETURN g1.Name as Group_Name1,g2.name  as Group_Name2, type(r2) as Permissions, n.Computer as Computer


MATCH p = shortestPath((g:Group)-[r:MemberOf|AdminTo|HasSession*1..]->(n:Computer)) RETURN p

MATCH (u1:User)
WITH u1
OPTIONAL MATCH (u1)-[r:AdminTo]->(c1:Computer)
WITH u1,COUNT(c1) as expAdmin
OPTIONAL MATCH (u1)-[r:MemberOf*1..]->(a:Group)-[r2:AdminTo]->(c2:Computer)
WITH u1,expAdmin,COUNT(DISTINCT(c2)) as unrolledAdmin
RETURN u1.name as Group_Name,expAdmin,unrolledAdmin, expAdmin + unrolledAdmin as totalAdmin
ORDER BY totalAdmin DESC

MATCH p=(m:User)-[r:AdminTo]->(n:Computer) RETURN m.name,n.name
MATCH p = (u:User)-[r1:MemberOf]->(g2:Group)-[r2:AdminTo]->(n:Computer) RETURN u.name,type(r1),g.name,n.name


User and groups Local admin rights 

MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN u1.name,n.name
MATCH p=(m:Group)-[r:AdminTo]->(n:Computer) MATCH (u1:User)-[r:AdminTo]->(n:Computer) RETURN m.name,n.name

User and Group Delegated Local Admin Rights

MATCH p = (g1:Group)-[r1:MemberOf*1..]->(g2:Group)-[r2:AdminTo]->(n:Computer) RETURN p
MATCH p=(m:User)-[r1:MemberOf*1..]->(g:Group)-[r2:AdminTo]->(n:Computer) RETURN m.name,

User and group derivative Local Admin Rights

MATCH p=shortestPath((m:User)-[r:HasSession|AdminTo|MemberOf*1..]->(n:Computer)) RETURN p
MATCH p = shortestPath((g:Group)-[r:MemberOf|AdminTo|HasSession*1..]->(n:Computer)) RETURN p



	List of Users to LocalAdmins (Explicit)
	MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN u1.name,n.name
	 
	List of Users to Local Admins with all the properties
	MATCH p=(u1:User)-[r:AdminTo]->(n:Computer) RETURN p
	 
	Explicit and unrolled users to computes
	MATCH (u1:User) WITH g OPTIONAL MATCH (u1)-[r:AdminTo]->(c1:Computer) WITH u1,COUNT(c1) as explicitAdmins OPTIONAL MATCH (u1)-[r:MemberOf*1..]->(a:User)-[r2:AdminTo]->(c2:Computer) WITH u1,explicitAdmins,COUNT(DISTINCT(c2)) as unrolledAdmins RETURN u1.name,explicitAdmins,unrolledAdmins, explicitAdmins + unrolledAdmins as totalAdmins ORDER BY totalAdmins DESC
	 
	List of users with delegated Local Admins
	MATCH p = (u:User)-[r1:MemberOf]->(g:Group)-[r2:AdminTo]->(n:Computer) RETURN u.name,type(r1),g.name,n.name


//6 NOT a domain controller that is trusted to perform unconstrained delegation (we have to remove the Domain controller) 

MATCH (c1:Computer)-[:MemberOf*1..]->(g:Group)
WHERE g.objectsid ENDS WITH "-516"
WITH COLLECT(c1.name) AS domainControllers
MATCH (c2:Computer {unconstraineddelegation:true})
WHERE NOT c2.name IN domainControllers
RETURN c2.name,c2.operatingsystem,c2.serviceprincipalnames
ORDER BY c2.name ASC


//7 Users with passwords last set  more than 180 days
MATCH (u:User) WHERE u.pwdlastset < (datetime().epochseconds - (180 * 86400)) and NOT u.pwdlastset IN [-1.0, 0.0] RETURN u,u.name

//8 Never logged on and account is still active
MATCH (n:User) WHERE n.lastlogontimestamp=-1.0 AND n.enabled=TRUE RETURN n.name ORDER BY n.name

//9. SPN for the computer contains the string "MSSQL" MATCH (c:Computer)
MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL")
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC

//10.SMB shares in their description fields
MATCH (n) WHERE n.description CONTAINS '\\\\' RETURN n.name, n.description

//11. Groups CanRDP on Computers
MATCH p=(m:Group)-[r:CanRDP]->(n:Computer) RETURN m.name,n.name
MATCH p=(u1:User)-[r:CanRDP]->(n:Computer) RETURN u1.name,n.namE



MATCH p=(n:User{passwordnotreqd:false}) RETURN n.name as name,n.displayname as ,n.enabled,n.passwordnotreqd,n.distinguishedname,n.email

MATCH p=(n:User{passwordnotreqd:false}) RETURN n.name as Name, n.displayname as Displayname, n.enabled as Active, n.passwordnotreqd as Passwordnotreqd, n.email as Email, n.distinguishedname as Distinguishedname


//12.Group ALC Permission

ALC: HasSession 

Group can do the HasSessionto computer
Group can do the HasSessionto user
Group  can do the HasSessionto group

MATCH p=(m:Group)-[r:AllExtendedRights]->(n:Computer) RETURN m.name as Group_Name,type(r) as ACL_Permission,n.name as Computer_Name
MATCH p=(m:Group)-[r:AllExtendedRights]->(u1:User) RETURN m.name as Group_Name,type(r) as ACL_Permission,u1.name as User_Name
MATCH p=(m:Group)-[r:AllExtendedRights]->(m1:Group) RETURN m.name as Group_Name,type(r) as ACL_Permission,m1.name as Group_Name2

User can do the HasSessionon computer
User can do the HasSessionon group
User can do the HasSessionon User

MATCH p=(u1:User)-[r:AllExtendedRights]->(n:Computer) RETURN u1.name as User_Name,type(r) as ACL_Permission,n.name as Computer_Name
MATCH p=(u1:User)-[r:AllExtendedRights]->(m:Group) RETURN u1.name as User_Name,type(r) as ACL_Permission,m.name as Group_Name
MATCH p=(u1:User)-[r:AllExtendedRights]->(u2:User) RETURN u1.name as User_Name,type(r) as ACL_Permission,u2.name as User_Name2

Computer can do the HasSessionon User
Computer can do the HasSessionon group
Computer can do the HasSessionon computer

MATCH p=(n:Computer)-[r:AllExtendedRights]->(u1:User) RETURN n.name as Computer_Name,type(r) as ACL_Permission,u1.name as User_Name
MATCH p=(n:Computer)-[r:HasSession]->(m:Group) RETURN n.name as Computer_Name,type(r) as ACL_Permission,m.name as Group_Name
MATCH p=(n:Computer)-[r:AllExtendedRights]->(n1:Computer) RETURN n.name as Computer_Name,type(r) as ACL_Permission,n1.name as Computer_Name2

ALC Permission list:

Default Edges: MemberOf, HasSession, AdminTo

ACL Edges AllExtendedRights, AddMember, ForceChangePassword, GenericAll, GenericWrite, Owns, WriteDacl, WriteOwner, ReadLAPSPassword, 
ReadGMSAPassword, AddKeyCredentialLink, WriteSPN, AddSelf

Containers Contains, GpLink

Special CanRDP, CanPSRemote ,ExecuteDCOM ,AllowedToDelegate ,AddAllowedToAct ,AllowedToAct ,SQLAdmin ,HasSIDHistory


MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' a RETURN H.name AS Computer_Name,H.operatingsystem AS Operatingsyste ,H.serviceprincipalnames AS ServicesRunning

MATCH p=(m:Group)-[r:HasSession]->(n:Computer) RETURN WHERE H.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' a RETURN H.name AS Computer_Name,H.operatingsystem AS Operatingsyste ,H.serviceprincipalnames AS ServicesRunning


MATCH (m:Group)-[r:HasSession]->(n:Computer) WHERE n.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN n.name
MATCH (u1:User)-[r:HasSession]->(n:Computer) WHERE n.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN n.name
MATCH (n:Computer)-[r:HasSession]->(u1:User) WHERE n.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN n.name,u1.name


MATCH (n:Computer) MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' MATCH p=allShortestPaths((n)-[r:HasSession*1..]->(m)) RETURN n.name



MATCH (c:Computer) WHERE ANY (x IN c.serviceprincipalnames WHERE toUpper(x) CONTAINS "MSSQL") MATCH (m:Group)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|CanPSRemote|AddAllowedToAct|AllowedToAct|SQLAdmin|HasSIDHistory|AZAddMembers|AZContains|AZContributor|AZGetCertificates|AZGetKeys|AZGetSecrets|AZGlobalAdmin|AZOwns|AZPrivilegedRoleAdmin|AZResetPassword|AZUserAccessAdministrator|AZAppAdmin|AZCloudAppAdmin|AZRunsAs|AZKeyVaultContributor|ReadGMSAPassword|AddSelf|WriteSPN|AddKeyCredentialLink*1..]->(n:Computer)
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC



MATCH (n:User) MATCH (m:Group) MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|CanPSRemote|AddAllowedToAct|AllowedToAct|SQLAdmin|HasSIDHistory|AZAddMembers|AZContains|AZContributor|AZGetCertificates|AZGetKeys|AZGetSecrets|AZGlobalAdmin|AZOwns|AZPrivilegedRoleAdmin|AZResetPassword|AZUserAccessAdministrator|AZAppAdmin|AZCloudAppAdmin|AZRunsAs|AZKeyVaultContributor|ReadGMSAPassword|AddSelf|WriteSPN|AddKeyCredentialLink*1..]->(m)) RETURN p

MATCH (u1:User)-[r:HasSession|AdminTo]->(n:Computer) WHERE n.operatingsystem =~ '.*(2000|2003|2003 R2|xp|vista|7|2008|2008 R2|2011|8|2000|me).*' RETURN u1.name as Username,TYPE(r) as ACL_Permission,n.name as Computer_Name

MATCH p=(m:User)-[r:GenericAll|GenericWrite|AllExtendedRights]->(n:Computer) where m.hasspn m,n

MATCH p=(m:User)-[r:GenericAll|GenericWrite|AllExtendedRights]->(n:Computer) where n.serviceprincipalnames=~ '.*['*' return m.name,n.name


MATCH p=(m:User)-[r:GenericAll|GenericWrite|AllExtendedRights]->(n:Computer) WHERE ANY (x IN n.serviceprincipalnames WHERE toUpper(x) CONTAINS 'MSSQL') RETURN m.name,n.name,n.serviceprincipalnames ORDER BY n.name ASC

MATCH p=(m:User)-[r:GenericAll|GenericWrite|AllExtendedRights]->(n:Computer) WHERE ANY (x IN n.serviceprincipalnames WHERE toUpper(x) CONTAINS 'HOST') RETURN m.name,n.name,n.serviceprincipalnames ORDER BY n.name ASC

MATCH p=(m:User)-[r:GenericAll|GenericWrite|AllExtendedRights|SQLAdmin*1..]->(n:Computer) WHERE ANY (x IN n.serviceprincipalnames WHERE toUpper(x) CONTAINS 'HOST') RETURN m.name,n.name,n.serviceprincipalnames ORDER BY n.name ASC

Match (u:User)-[:MemberOf]->(g:Group) WHERE g.name CONTAINS "VPN" return u.name,g.name


All Services

MATCH (c:Computer)
WHERE ANY (x IN c.serviceprincipalnames WHERE toLower(x) CONTAINS "guww")
RETURN c.name,c.serviceprincipalnames
ORDER BY c.name ASC
