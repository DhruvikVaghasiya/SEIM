# 🔐 Windows Security Event Monitoring with Splunk

This project sets up Splunk Enterprise to collect, analyze, and alert on key Windows Security Event Logs (e.g., user account creation, group membership changes) in real-time.

---

## 📌 Objective

To monitor critical Windows events such as:
- **4720**: User account creation
- **4728**: Addition to a global group
- **4732**: Addition to a local group

This is part of a broader initiative to enhance visibility into Active Directory changes and user management activity for security auditing.

---

## 🛠️ Prerequisites

- Splunk Enterprise (or Splunk Free)
- Universal Forwarder installed on Windows machine
- Windows Event Log collection enabled
- Admin privileges on Windows to read Security logs

---

## 📥 Data Ingestion Setup

1. **Install Universal Forwarder** on Windows host.
2. Configure inputs:
    ```ini
    [WinEventLog://Security]
    disabled = 0
    ```
3. Configure outputs to Splunk Indexer (or Heavy Forwarder):
    ```ini
    [tcpout]
    defaultGroup = default-autolb-group

    [tcpout:default-autolb-group]
    server = <your_splunk_indexer_ip>:9997
    ```

4. **Verify connection** using:
    ```powershell
    netstat -an | findstr 9997
    ```

5. **On Splunk Web**, confirm logs are arriving:
    ```
    index=wineventlog sourcetype="WinEventLog:Security"
    ```

---

## 🔎 SPL Queries Used

```spl
index=wineventlog (EventCode=4720 OR EventCode=4728 OR EventCode=4732)
| table _time, host, EventCode, Account_Name, Target_Username, Message
