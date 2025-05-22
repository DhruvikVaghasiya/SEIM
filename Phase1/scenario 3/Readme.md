# Scenario 3: Lateral Movement Attempt via RDP (T1021.001)

## 🛠️ Description
This scenario simulates a lateral movement attempt using Remote Desktop Protocol (RDP) between two Windows VMs. The attacker attempts multiple failed RDP logins before successfully logging in from a non-standard internal IP address, mimicking a common lateral movement technique.

---

## 🎯 Objective
- Detect failed and successful RDP logins (Event IDs 4625 and 4624).
- Identify if a privileged account successfully logs in from a **non-standard internal IP**.
- Correlate successful login with earlier failed attempts.
- (Bonus) Apply GeoIP enrichment if the login originates from a VPN.

---

## 🧪 Lab Environment
- **Windows 10/11 VM (Attacker)**
- **Windows 10/11 or Server VM (Target)**
- **Ubuntu VM with Splunk Enterprise and Winlogbeat Forwarders**
- Optional VPN or different internal IP subnet to simulate anomaly

---

## 🔧 Step-by-Step Execution

### ✅ 1. Prepare the Environment
- Enable **Remote Desktop (RDP)** on both Windows VMs:
  - `Settings > System > Remote Desktop > Enable`
- Enable Security Event Logging:
  - Run `secpol.msc` → Audit Policy → Enable:
    - `Audit logon events` (Success/Failure)
    - `Audit account logon events` (Success/Failure)

### ✅ 2. Simulate the Attack
- From the **Attacker VM**, open RDP (`mstsc`) and connect to the **Target VM**:
  - Perform **3-5 failed logins** with incorrect credentials.
  - Then perform **a successful login** using valid admin credentials.
  - Use a **different subnet IP** or VPN if possible.

### ✅ 3. Log Forwarding to Splunk
- Ensure **Winlogbeat** or **Splunk Universal Forwarder** is configured on both VMs.
- Logs should be sent to Splunk from the **Security event channel**.
- Event IDs involved:
  - **4625** = Failed RDP login
  - **4624** = Successful RDP login (LogonType 10 = Remote Interactive)

### ✅ 4. Splunk Detection Queries

#### 🔍 A. Detect Failed RDP Logins
```spl
index=winlogbeat OR index=wineventlog EventCode=4625 LogonType=10
| stats count by Account_Name, host, IpAddress, _time
