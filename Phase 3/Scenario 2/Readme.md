Scenario 2: Lateral Movement via RDP Brute Force

Objective

Simulate a brute-force attack against a Remote Desktop Protocol (RDP) service using stolen credentials. Analyze failed and successful login attempts to detect and respond to lateral movement.

Attack Narrative

An attacker has compromised a low-privilege user account and is attempting to move laterally within the network by brute-forcing RDP access to internal systems using known or guessed credentials.

Setup and Environment

Attacker Machine: Kali Linux (192.168.1.15)

Target Machine: Windows 10 (192.168.1.13), Hostname: DESKTOP-PERTPN8

SIEM: Splunk running on Ubuntu, receiving logs from Windows and Sysmon

Log Forwarders: Winlogbeat + Sysmon installed on Windows

Tools Used

Hydra (Kali)

RDP Service on Windows 10

Event Viewer & Splunk

Event IDs: 4625, 4624 (Security Log), Sysmon Event ID 3

Step-by-Step Guide

Step 1: Brute Force Simulation with Hydra

hydra -t4 -v -f -l helpdesk -P pass.txt rdp://192.168.1.13

-t4 = 4 parallel tasks

-f = stop when a valid password is found

-l helpdesk = target username

-P pass.txt = password list

rdp://192.168.1.13 = target IP

If Hydra finds a password, simulate a successful RDP login manually or with xfreerdp.

Step 2: Log Monitoring in Splunk

Event IDs to Monitor

4625: Failed login attempt (Security Log)

4624: Successful login (especially LogonType=10 for RDP)

Sysmon 3: Network connection

Search Queries

index=winlogs host="DESKTOP-PERTPN8" EventCode=4625
index=winlogs host="DESKTOP-PERTPN8" EventCode=4624 LogonType=10

Step 3: Alert Creation

Create a Splunk alert for multiple failed login attempts from a single IP.

Trigger alert when 5+ EventCode 4625 logs appear within 1 minute.

Step 4 (Optional): Lateral Movement Simulation

Login to Windows using cracked credentials via RDP.

Perform lateral movement (e.g., create user, open command prompt, execute scripts).

Monitor resulting logs in Splunk using Event IDs and Sysmon data.

Detection Strategy

EventCode 4625 (failure): Look for brute-force indicators (e.g., rapid repeated logins)

EventCode 4624 with LogonType=10: Indicates successful RDP logins

Sysmon ID 3: Track RDP connections

Correlate Source IP: Same source IP across multiple login attempts

Mitigation and Recommendations

Enable account lockout policies

Monitor for excessive 4625 events

Use MFA for RDP access

Implement rate limiting and RDP honeypots

Conclusion

This scenario demonstrated how an attacker can perform brute-force attacks to gain lateral access via RDP, and how a defender can detect and respond using event correlation in Splunk. This simulation enhances blue team readiness against brute-force based lateral movement.

Tags

RDP Brute Force Lateral Movement Splunk Event ID 4625 Hydra SIEM APT Simulation Cybersecurity Internship
