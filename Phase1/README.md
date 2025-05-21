# Phase 1 – Splunk Internship: Brute Force Detection Alert

## ✅ Objective
To detect brute force login attempts by monitoring multiple failed login logs using Splunk and trigger alerts.

---

## 🔍 Alert: Brute Force Detection – Multiple Failed Logins

**SPL Query:**
```spl
index=your_index sourcetype=your_sourcetype action=failure
| stats count by src_ip
| where count > 5
