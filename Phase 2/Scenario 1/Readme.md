# Scenario 1: Privilege Escalation

## 🧪 Simulation Steps (on Windows VM)
1. Open CMD as Admin
2. `net user backdoor Passw0rd! /add` (Event ID: 4720)
3. `net localgroup administrators backdoor /add` (Event ID: 4728)
4. `runas /user:backdoor cmd`
5. Check: `whoami` + `net session` (Success = Privilege Escalation)
6. Event ID 4672 appears for backdoor login

## 🔍 Detection
- Event 4720: New user created
- Event 4728: User added to local admin group
- Event 4672: Special privileges assigned

## 💡 Detection Strategy
- Alert on new user accounts created during odd hours
- Alert if non-IT user gets admin rights
- Investigate logins with Event ID 4672

## 🛡️ Recommendations
- Use LAPS and limit local admin rights
- Review admin group changes daily
- Enable logging (Sysmon, Winlogbeat)
