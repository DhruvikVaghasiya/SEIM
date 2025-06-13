# 🛡️ Scenario 5: Credential Dumping and Exfiltration using Mimikatz

## 🎯 Objective
Simulate an Advanced Persistent Threat (APT) scenario where the attacker uses **Mimikatz** to dump credentials from the LSASS process and exfiltrates them over HTTPS. This guides defenders to detect and analyze credential dumping activity using **Sysmon**, **Windows logs**, and **Splunk**.

---

## 🔧 Environment Setup
- **Attacker Machine**: Kali Linux
- **Target Machine**: Windows 10/11 (with Sysmon + Winlogbeat configured)
- **SIEM**: Splunk running on Ubuntu, collecting logs from Windows host
- **Sysmon Version**: v13+
- **Sysmon Config**: Includes Event ID 10 (Process Access)

---

## 🧩 Steps Performed

### ✅ Step 1: Enable LSASS Access Logging via Sysmon

- Make sure **Sysmon is configured** to capture **Event ID 10** for process access.
- Your Sysmon config should contain:

```xml
<EventFiltering>
  <RuleGroup name="process_access" groupRelation="or">
    <ProcessAccess onmatch="include">
      <TargetImage condition="is">C:\Windows\System32\lsass.exe</TargetImage>
    </ProcessAccess>
  </RuleGroup>
</EventFiltering>
```
Restart Sysmon to apply:
```
sysmon64.exe -c sysmon_config.xml
```
✅ Step 2: Enable Script Block Logging for PowerShell (optional)
```
Set-ItemProperty -Path HKLM:\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging -Name EnableScriptBlockLogging -Value 1 -Force
```
✅ Step 3: Execute Credential Dump Using Mimikatz
Option 1: Meterpreter Post Module (MSF)
```
use post/windows/gather/credentials/mimikatz
```
Option 2: Invoke-Mimikatz in PowerShell (Manually)
```
Invoke-Mimikatz -Command '"sekurlsa::logonpasswords"'
⚠️ This attempts to read LSASS memory and triggers Event ID 10.
```
✅ Step 4: Detect in Splunk
🔍 Query 1: Access to LSASS by Suspicious Process
```
index=winlogs EventCode=10 TargetImage="C:\\Windows\\System32\\lsass.exe"
| stats count by SourceImage, TargetImage, GrantedAccess, Computer, _time
```
Look for:

Unusual SourceImage like powershell.exe, mimikatz.exe

GrantedAccess like 0x1410, 0x1010 indicating sensitive memory access

🔍 Query 2: Mimikatz or Related Execution
```
index=winlogs (EventCode=1 OR EventCode=10)
(Image="*mimikatz.exe" OR Image="*powershell.exe")
| stats count by Image, CommandLine, Computer, _time
```
🛑 Indicators of Compromise (IOCs)
lsass.exe accessed by PowerShell or other non-standard tools

Suspicious command-line with keywords: sekurlsa, logonpasswords

GrantedAccess=0x1410 (read+write+operation access)
