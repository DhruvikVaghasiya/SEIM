# Scenario 2: Suspicious Logon Times – After-Hours Admin Activity (T1078.004)

## 📌 Overview

This scenario simulates a privileged user (administrator) logging into a Windows machine **outside of business hours** (e.g., 2:30 AM) and accessing sensitive files or services. The goal is to detect and alert such activity using Splunk on Ubuntu.

---

## 🧪 Attack Chain

- **Step 1:** Admin logs in at **2:30 AM**
- **Step 2:** Admin accesses sensitive folders or services
- **Step 3:** Admin logs out

---

## 🎯 Objective

Detect **after-hours privileged account activity** and generate alerts in Splunk for logins occurring **outside of business hours** (9 AM – 7 PM).

---

## 🖥️ Lab Setup

| Component        | Description                         |
|------------------|-------------------------------------|
| Kali Linux       | Optional (for red team activity)    |
| Windows 10 VM    | Victim machine with admin login     |
| Ubuntu VM        | Runs **Splunk Enterprise Server**   |

---

## 🔧 Step-by-Step Execution

### ✅ Step 1: Create an Admin Account on Windows

```powershell
net user "admin-afterhours" "SecurePass123" /add
net localgroup "Administrators" "admin-afterhours" /add

✅ Step 2: Simulate After-Hours Login
Temporarily change system time to 2:30 AM for testing:

cmd
Copy
Edit
time 02:30:00
⚠️ Reset system time after test.

✅ Step 3: Perform Suspicious Activity
While logged in as admin-afterhours, access critical folders:

C:\Users\Administrator\Documents

C:\Windows\System32\config\

Use PowerShell to interact with services:

powershell
Copy
Edit
Get-Service
Then log out.

📥 Step 4: Ensure Log Forwarding to Splunk
Verify the Splunk Universal Forwarder is installed on Windows 10 and configured to send logs.

Check the config:

perl
Copy
Edit
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
Ensure it includes:

ini
Copy
Edit
[WinEventLog://Security]
disabled = 0

[WinEventLog://System]
disabled = 0
Restart the Splunk Forwarder:

cmd
Copy
Edit
sc stop splunkforwarder
sc start splunkforwarder
🔍 Step 5: Splunk Detection Query
Login to Splunk Web Interface (http://localhost:8000) and use this query:

spl
Copy
Edit
index=wineventlog sourcetype="WinEventLog:Security" EventCode=4624 
Account_Name="admin-afterhours"
| eval Hour=strftime(_time, "%H")
| where Hour < 9 OR Hour > 19
🚨 Step 6: Set Up Alert
Save the search as a Scheduled Alert

Trigger condition: If number of results > 0

Set alert action: Email / Slack / Log to Dashboard

💡 Enhancement (Optional)
Tag Privileged Users using a lookup table or custom field (e.g., Domain Admins, Local Admins)

Detect multiple after-hour logins with aggregation:

spl
Copy
Edit
... same base query ...
| stats count by Account_Name, host
| where count > 1
