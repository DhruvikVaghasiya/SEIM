# 🔐 Scenario 1: Brute Force Followed by Privileged Login (T1110 + T1078)

## 📖 Overview

This lab simulates a **Brute Force Attack (T1110)** followed by a **Privileged Login (T1078)** on a Windows 10 system. The goal is to perform the attack from Kali Linux, log the events on Windows, and detect them using a Splunk SIEM deployed on Ubuntu. This scenario helps understand real-world attacker behavior and how to detect it using log correlation and SIEM alerts.

---

## 🧪 MITRE ATT&CK Techniques

| Technique Name                   | ID     |
|----------------------------------|--------|
| Brute Force                      | T1110  |
| Valid Accounts (Privileged Login) | T1078  |

---

## 💻 Lab Setup

| Machine         | Role                        |
|----------------|-----------------------------|
| Kali Linux      | Attacker                    |
| Windows 10      | Victim (Target)             |
| Ubuntu Server   | Splunk Enterprise (SIEM)    |

---

## 🔧 Configuration

### 🔹 On Windows 10:
1. Enable **Audit Logon Events**:
   - `secpol.msc` → Local Policies → Audit Policy → Enable:
     - Audit Logon Events: **Success and Failure**
     - Audit Account Logon Events: **Success and Failure**

2. Create a user:
   ```powershell
   net user testuser test123 /add
   net localgroup administrators testuser /add

