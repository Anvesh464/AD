Here is the raw format of your content, properly formatted for GitHub markdown (`.md`):

```markdown
# System Information and Configuration

## OS Name and Version
To get the OS name and version:
```bash
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

## Extract Patches and Updates
To extract patches and updates:
```bash
wmic qfe
```

## Architecture
To get the system architecture:
```bash
wmic os get osarchitecture || echo %PROCESSOR_ARCHITECTURE%
```

## List All Environment Variables
To list all environment variables:
```bash
set
```
Or using PowerShell:
```bash
Get-ChildItem Env: | ft Key,Value
```

## List All Drives
To list all drives:
```bash
wmic logicaldisk get caption || fsutil fsinfo drives
```

For detailed information about drives:
```bash
wmic logicaldisk get caption,description,providername
```

Or using PowerShell:
```bash
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root
```
```
