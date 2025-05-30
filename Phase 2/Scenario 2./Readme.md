# 🛡️ Scenario 2: Lateral Movement via SMB or PsExec (MITRE ATT&CK T1021.002)

## 📌 Objective

Simulate and detect lateral movement using remote administration tools like PsExec over SMB. Focus is on identifying unauthorized remote execution and correlating events via Sysmon and Windows logs.

---

## ⚔️ Attack Simulation

**Tool Used**: [Impacket `psexec.py`](https://github.com/fortra/impacket)

**Attacker Machine**: Kali Linux (192.168.1.10)  
**Victim Machine**: Windows 10 (192.168.1.16)

### 🔨 Command Executed on Kali:
```bash
impacket-psexec windows:Password123@192.168.*.**
