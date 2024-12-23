# Network Enumeration

This section contains commands for gathering network-related information, such as interfaces, IP configuration, DNS, routing tables, ARP tables, firewall configuration, network shares, and SNMP settings.

---

## List All Network Interfaces, IP, and DNS

To list all network interfaces, IP configuration, and DNS settings:

- **CMD (Windows Command Prompt)**:
  ```bash
  ipconfig /all
  ```

- **PowerShell**:
  ```powershell
  Get-NetIPConfiguration | ft InterfaceAlias, InterfaceDescription, IPv4Address
  ```

- **PowerShell**:
  ```powershell
  Get-DnsClientServerAddress -AddressFamily IPv4 | ft
  ```

- **CMD (Windows Command Prompt)**:
  ```bash
  nslookup.exe 192.168.1.1
  ```

---

## List Current Routing Table

To list the current routing table:

- **CMD (Windows Command Prompt)**:
  ```bash
  route print
  ```

- **PowerShell**:
  ```powershell
  Get-NetRoute -AddressFamily IPv4 | ft DestinationPrefix, NextHop, RouteMetric, ifIndex
  ```

---

## List the ARP Table

To list the ARP table:

- **CMD (Windows Command Prompt)**:
  ```bash
  arp -A
  ```

- **PowerShell**:
  ```powershell
  Get-NetNeighbor -AddressFamily IPv4 | ft ifIndex, IPAddress, LinkLayerAddress, State
  ```

---

## List All Current Connections

To list all current network connections:

- **CMD (Windows Command Prompt)**:
  ```bash
  netstat -ano
  ```

---

## List Firewall State and Current Configuration

To list the state and configuration of the firewall:

- **CMD (Windows Command Prompt)**:
  ```bash
  netsh advfirewall firewall dump
  ```

- **CMD (Windows Command Prompt)**:
  ```bash
  netsh firewall show state
  ```

- **CMD (Windows Command Prompt)**:
  ```bash
  netsh firewall show config
  ```

---

## List Firewall's Blocked Ports

To list the firewall's blocked ports:

- **PowerShell**:
  ```powershell
  $f=New-object -comObject HNetCfg.FwPolicy2;$f.rules | where {$_.action -eq "0"} | select name, applicationname, localports
  ```

---

## Disable Firewall

To disable the firewall:

- **CMD (Windows Command Prompt)**:
  ```bash
  netsh firewall set opmode disable
  ```

- **CMD (Windows Command Prompt)**:
  ```bash
  netsh advfirewall set allprofiles state off
  ```

---

## List All Network Shares

To list all network shares:

- **CMD (Windows Command Prompt)**:
  ```bash
  net share
  ```

---

## SNMP Configuration

To check SNMP configuration:

- **CMD (Windows Command Prompt)**:
  ```bash
  reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP /s
  ```

- **PowerShell**:
  ```powershell
  Get-ChildItem -path HKLM:\SYSTEM\CurrentControlSet\Services\SNMP -Recurse
  ```

---
```
