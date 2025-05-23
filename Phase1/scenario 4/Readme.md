# Scenario 4: Splunk IOWait Troubleshooting on Windows

## 📌 Objective
To identify and troubleshoot IOWait-related issues in a local Splunk deployment on Windows, investigate root causes, analyze performance bottlenecks, and ensure reliable log visibility through Splunk.

---

## 🧠 Background
IOWait is the time the CPU waits for I/O operations (e.g., disk read/write). When IOWait is high, it causes Splunk to delay or drop indexing/searching activities. In this scenario, we encountered degraded performance and missing logs in Splunk due to excessive IOWait.

---

## 🔧 Environment

- **OS**: Windows 11 (host)
- **User**: `testuser`
- **Splunk Version**: Enterprise Free (local deployment)
- **Monitoring Source**: Winlogbeat
- **Splunk Input**: File Monitor Input

---

## 🧪 Steps Performed

1. **Accessed Splunk Web Interface**  
   - Logged in as `testuser` with password `test123`
   - Navigated to `Activity → Health Status`

2. **Identified Critical Alert: IOWait**  
   - Error under `Resource Usage → IOWait` showed:
     - System IOWait exceeded red threshold of 3
     - Sum of top 3 per-CPU IOWait exceeded threshold of 15

3. **Analyzed System Load**
   - Opened Task Manager and Resource Monitor on Windows
   - Verified high disk I/O usage, likely due to simultaneous log ingestion/searches

4. **Reduced Search and Input Load**
   - Disabled non-essential saved searches in Splunk
   - Modified Winlogbeat to reduce event frequency

5. **Restarted Splunk Services**
   - Used GUI restart from Splunk Web
   - Confirmed health alert was cleared post-action

6. **Validated Fix**
   - Executed manual search using:
     ```
     index=* OR index=_* | stats count by index, source, sourcetype
     ```
   - Confirmed logs were appearing properly from Winlogbeat

---

## 📈 Search Command Used

```spl
index=* OR index=_* | stats count by index, source, sourcetype
