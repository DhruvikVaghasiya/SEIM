🔐 Scenario 3: Lateral Movement Attempt via RDP (T1021.001)
📖 Overview
This scenario simulates a lateral movement attempt where an attacker uses Remote Desktop Protocol (RDP) to move laterally within a network. Since only one Windows 10 VM is available, we simulate the attack using Kali Linux as the attacker machine.

🎯 Objective
Simulate failed RDP login attempts followed by a successful login.

Trigger alerts in Splunk for:

Successful RDP login from a non-standard internal IP.

Correlate previous failed attempts with a successful login.

🧪 Lab Environment
Component	Configuration/Use
Kali Linux	Used to initiate RDP brute-force and successful login
Windows 10 VM	Target system (RDP enabled)
Ubuntu + Splunk	SIEM log collection and detection

🔧 Configuration Steps
1. ✅ Enable RDP on Windows 10 VM
Go to: System Properties > Remote → Enable:

“Allow remote connections to this computer”

Allow Remote Desktop through Windows Defender Firewall:

Control Panel > Windows Defender Firewall > Allow an app

2. 🛡️ Configure Security Logging
On Windows 10 VM:

Open secpol.msc → Local Policies > Audit Policy:

Enable:

Audit logon events → Success, Failure

Audit account logon events → Success, Failure

3. 📦 Install and Configure Winlogbeat
Install Winlogbeat to forward Windows logs to Splunk:

Configure winlogbeat.yml:

yaml
Copy
Edit
winlogbeat.event_logs:
  - name: Security
    ignore_older: 72h

output.logstash:
  hosts: ["<Ubuntu_IP>:5044"]
Start the Winlogbeat service:

bash
Copy
Edit
Start-Service winlogbeat
✅ Ensure the Splunk server is configured to receive logs from Winlogbeat (via Logstash or direct input).

🚨 Simulating the Attack
Step 1: Install RDP Client on Kali
bash
Copy
Edit
sudo apt update
sudo apt install freerdp2-x11 -y
Step 2: Failed RDP Attempts
Use incorrect credentials multiple times:

bash
Copy
Edit
xfreerdp /u:Admin /p:wrongpass /v:<Windows10_VM_IP>
Step 3: Successful Login
Try with valid credentials:

bash
Copy
Edit
xfreerdp /u:Admin /p:CorrectPassword /v:<Windows10_VM_IP>
✅ You’ve now simulated lateral movement with multiple failed attempts followed by success.

📊 Detection in Splunk
A. Failed RDP Attempts
spl
Copy
Edit
index=winlogbeat OR index=wineventlog EventCode=4625 LogonType=10
| stats count by Account_Name, IpAddress, _time
B. Successful RDP Logins
spl
Copy
Edit
index=winlogbeat OR index=wineventlog EventCode=4624 LogonType=10
| stats count by Account_Name, IpAddress, _time
C. Correlate Failed + Success Attempts
spl
Copy
Edit
index=winlogbeat OR index=wineventlog (EventCode=4625 OR EventCode=4624) LogonType=10
| stats values(EventCode) as events, min(_time) as first_seen, max(_time) as last_seen by Account_Name, IpAddress
| where "4625" in events AND "4624" in events
💡 Bonus: Geolocation (Optional)
Use Splunk's IP lookup or external threat intel API to tag non-standard internal or VPN IPs.

✅ Outcome
Successfully simulated RDP brute-force and lateral movement from Kali Linux to Windows 10 VM.

Logs captured and analyzed in Splunk.

Correlated successful login with previous failed attempts from a suspicious IP.
