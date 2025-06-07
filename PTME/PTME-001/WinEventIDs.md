# Windows Security Event IDs for Threat Detection

| Event ID | Log Source / Category | Description |
|----------|------------------------|-------------|
| 4624     | Security               | Successful logon |
| 4625     | Security               | Failed logon |
| 4647     | Security               | User initiated logoff |
| 4634     | Security               | Logoff event |
| 4648     | Security               | Logon using explicit credentials (e.g. runas, mimikatz) |
| 4672     | Security               | Special privileges assigned to new logon |
| 4688     | Security               | A new process has been created |
| 4689     | Security               | A process has exited |
| 4697     | Security               | A service was installed in the system |
| 4719     | Security               | System audit policy was changed |
| 4720     | Security               | A user account was created |
| 4722     | Security               | A user account was enabled |
| 4723     | Security               | A user changed their password |
| 4724     | Security               | An attempt was made to reset an account’s password |
| 4725     | Security               | A user account was disabled |
| 4726     | Security               | A user account was deleted |
| 4767     | Security               | A user account was unlocked |
| 4670     | Security               | Permissions on an object were changed |
| 4663     | Security               | An attempt was made to access an object (file/folder) |
| 4656     | Security               | A handle to an object was requested |
| 4776     | Security               | NTLM authentication attempt |
| 4768     | Security               | Kerberos authentication ticket (TGT) requested |
| 4769     | Security               | Kerberos service ticket (TGS) requested |
| 4770     | Security               | Kerberos ticket renewed |
| 4771     | Security               | Kerberos pre-authentication failed |
| 4798     | Security               | User’s local group membership enumeration |
| 4799     | Security               | Enumerated global group membership |
| 4649     | Security               | A replay attack was detected |
| 1102     | Security               | The audit log was cleared |
| 7045     | System                 | A service was installed on the system |
| 4902     | Security               | The per-user audit policy was changed |
| 5156     | Security (Firewall)    | Allowed network connection |
| 5158     | Security (Firewall)    | Network connection attempted |
| 4103     | PowerShell Operational | PowerShell command line logged |
| 4104     | PowerShell Operational | PowerShell script block logged (base64-decoded content) |
| 4105     | PowerShell Operational | Script blocked by execution policy |
| 4106     | PowerShell Operational | New PowerShell engine state (Execution Policy) |
| 400      | Windows PowerShell     | Engine lifecycle event (started) |
| 403      | Windows PowerShell     | Engine lifecycle event (stopped) |
| 600      | Windows PowerShell     | Provider lifecycle (e.g., loaded) |
| 800      | Windows PowerShell     | Execution of a command |
| 1116     | Defender Operational   | Malware detected |
| 1121     | Defender Operational   | Malware action performed (e.g., removed, quarantined) |
| 5860     | WMI Activity           | WMI consumer registered |
| 5858     | WMI Activity           | WMI filter triggered |
| 5861     | WMI Activity           | WMI binding event |
| 1        | Sysmon                 | Process creation |
| 3        | Sysmon                 | Network connection initiated |
| 7        | Sysmon                 | Image loaded (DLL, EXE) |
| 11       | Sysmon                 | File created |
| 13       | Sysmon                 | Registry value set |
| 22       | Sysmon                 | Named pipe created |

#### What cybersecurity analyst should know about it?
Windows does not log everything by default. You must enable:
- Audit Process Creation (for 4688 + command line logging)
- Audit Logon Events
- Audit Object Access
- Enable Script Block Logging, Module Logging in PowerShell GPO

<b>Sysmon Logs</b>  
It provides detailed information about process creations, network connections, and changes to file creation time. It is much more granular than default Windows logging. Example informations from <a href="https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon">System Monitor</a> logs:  
- Process parent-child relationships
- Network connections
- Registry changes
- File writes
  
<b>Sigma rules</b>  
Using <a href="https://github.com/SigmaHQ/sigma">**Sigma**</a> rules (generic signature format to describe detection rules based on Windows Event IDs) is a good and common practice.  

<b>Logs tagging</b>  
Most used framework in cybersecurity is MITRE ATT&CK. Logs should be tagged in line with it.

### KQL (Kusto Query Language)
KQL is a powerful query language used to search, analyze, and visualize structured logs. It's similar to SQL but optimized for log and telemetry data — especially time-series and security logs. Let's give an example query (brute force detection):  
<pre><code>SecurityEvent
| where EventID == 4625  
| summarize FailedLogonAttempts = count() by TargetUserName, IpAddress, bin(TimeGenerated, 15m)  
| where FailedLogonAttempts > 10  
| order by FailedLogonAttempts desc</code></pre>  

KQL Specific Concepts:

| Concept     | Meaning                                                       |
| :-----------: | :-------------------------------------------------------------: |
| Table     | The log source (e.g., `SecurityEvent`, `DeviceProcessEvents`) |
| Project   | Select specific columns                                       |
| Where     | Filter rows (like `WHERE` in SQL)                             |
| Summarize | Group and aggregate                                           |
| Join      | Combine data from multiple tables                             |
| Extend    | Add calculated/custom fields                                  |
| Parse     | Extract fields from strings                                   |
| Let       | Create reusable variables                                     |
| Order by  | Sort results                                                  |  

Some of them might look like SQL (especially "Order by", "Join", "Where") so learning KQL is not a challenge, but helps a lot. 
