# Scenario 4: DNS Query Logging using Sysmon (MITRE T1046 – Network Service Discovery)

## 🧠 Objective

This scenario demonstrates how attackers can perform network service discovery by leveraging DNS queries. Our goal is to detect such activity using Sysmon and visualize it in Splunk.

We aim to:

- Enable DNS query logging via Sysmon
- Simulate DNS lookups (e.g., `nslookup google.com`)
- Detect and analyze these DNS events in Sysmon logs (Event ID 22)
- View the logs in Splunk

---

## 🛠️ Step-by-Step Implementation

### ✅ Step 1: Enable DNS Logging in Sysmon

Ensure you're using **Sysmon v11 or higher**. Download from [Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).

1. Create or update your `sysmon_config.xml` with the following rule:
```xml
<Sysmon schemaversion="4.50">
  <EventFiltering>
    <DnsQuery onmatch="include" />
  </EventFiltering>
</Sysmon>
```

2. Trigger DNS Events
Run the following command in cmd or PowerShell:
```
nslookup google.com
nslookup facebook.com
nslookup github.com
   ```
3. Verify Logs in Event Viewer
```
Open Event Viewer

Navigate to:
Applications and Services Logs → Microsoft → Windows → Sysmon → Operational

Look for Event ID 22 (DNS Query)

```

4. View Logs in Splunk
n the Splunk search bar, run:
```
EventCode=22
```
