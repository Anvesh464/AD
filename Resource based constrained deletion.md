In **Active Directory (AD)**, **Resource-Based Constrained Delegation (RBCD)** is a feature introduced in **Windows Server 2012** that allows you to configure which services can delegate authentication on behalf of a user, but with a different twist compared to traditional constrained delegation. Instead of setting the delegation permissions on the service account, the delegation permissions are set on the **target service** (the resource).

### **GenericAll and GenericWrite Permissions and Their Role in Resource-Based Constrained Delegation:**

In Active Directory, the **GenericAll** and **GenericWrite** permissions are both powerful permissions that can give an attacker control over an object or resource in AD. Here's a breakdown:

1. **GenericAll**:
   - **GenericAll** is the most powerful permission in Active Directory, essentially giving **full control** over the object.
   - If a user or group has **GenericAll** permission on a resource (e.g., a service account), they can modify, delete, or change that resource in almost any way, including altering its **delegation settings**.

2. **GenericWrite**:
   - **GenericWrite** allows the ability to modify specific attributes of an object, but not full control over all aspects of the object.
   - For instance, a user with **GenericWrite** permissions on a service account could modify the **`msDS-AllowedToDelegateTo`** attribute, which controls what services that account can delegate authentication to, and this would allow an attacker to add additional services for delegation.

### **Resource-Based Constrained Delegation (RBCD)**:
In **RBCD**, the **delegation settings** are configured **on the target resource**, not on the service account making the request. Specifically, the target service (resource) is configured with the **`msDS-AllowedToActOnBehalfOfOtherIdentity`** attribute, which allows a service to delegate authentication to another service or server.

### **How GenericAll and GenericWrite Permissions Work in RBCD**:

- When a user or service has **GenericAll** or **GenericWrite** permissions on a resource object, they can potentially modify the **`msDS-AllowedToActOnBehalfOfOtherIdentity`** attribute, thereby altering which service accounts can delegate their credentials to that resource.
- In RBCD, if an attacker gains **GenericAll** or **GenericWrite** permissions on a target resource, they can manipulate the **delegation settings**, giving a service account the ability to act on behalf of any user and potentially escalate privileges. 
- For example, if an attacker has **GenericAll** or **GenericWrite** permissions on a service like `SQLServer`, they can configure the service to accept delegation from any service, including potentially the **Domain Administrator** account.

### **The Role of GenericAll and GenericWrite as Resource Owners**:
If a user or service has **GenericAll** or **GenericWrite** permissions on a target resource (service), they are essentially the "resource owner" because they can change its properties, including delegation permissions. In the context of **RBCD**, this means they can define which services can delegate authentication on behalf of a user and are, therefore, able to escalate privileges.

### **In Summary**:
- **GenericAll** and **GenericWrite** permissions give an entity the power to modify the delegation configuration of a service, potentially enabling unauthorized access and privilege escalation.
- With **RBCD**, if an attacker gains **GenericAll** or **GenericWrite** permissions on a resource (e.g., a service account), they can manipulate its **delegation settings**, essentially making them the "resource owner."
- This can lead to privilege escalation, as they can configure the resource to accept delegation from any service, including a compromised service or an attacker-controlled service.

In essence, **GenericAll** and **GenericWrite** permissions on a target resource make the user or group controlling the resource a "resource owner," enabling them to modify its delegation settings and potentially escalate privileges within the domain.

![image](https://github.com/user-attachments/assets/ccc60110-dfd4-4640-bca4-4d8cadd7211d)

![image](https://github.com/user-attachments/assets/f0d503bf-53e6-4ee7-9f26-09a75dbe797a)

![image](https://github.com/user-attachments/assets/c85fefd1-eefe-45d9-b02e-f5219aaf28d1)

![image](https://github.com/user-attachments/assets/dcab074a-92d7-48c1-be69-336da0e783f0)

## Ciadmin we got via Jenkin server compromise

![image](https://github.com/user-attachments/assets/f4dca890-e0d2-400e-9182-38c4f600c77e)

![image](https://github.com/user-attachments/assets/e0e5e8bf-0487-43aa-9b21-7087aa0e8475)

![image](https://github.com/user-attachments/assets/a32c1044-b9b2-45d8-8831-d6bd862d01d5)

![image](https://github.com/user-attachments/assets/45f0894f-195a-4d06-879b-88c5a307142a)

![image](https://github.com/user-attachments/assets/d48a0fc9-fb1b-474c-9e5e-085864849e3d)

![image](https://github.com/user-attachments/assets/85af6f4b-bdec-47be-92b5-22466d5c0297)

![image](https://github.com/user-attachments/assets/bcb10225-0bea-4f25-8c7e-71ac2d50eea9)

![image](https://github.com/user-attachments/assets/f377f75a-df26-4ac3-900c-095fc3bd87b6)

![image](https://github.com/user-attachments/assets/4d12d56d-2e73-4915-8d1e-29db2a383662)


