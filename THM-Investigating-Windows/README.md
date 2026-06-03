# TryHackMe — Investigating Windows

**Platform:** TryHackMe  
**Difficulty:** Easy  
**Category:** Digital Forensics / Incident Response  
**Date:** June 2026

---

## Overview

A Windows host has been compromised. The objective is to investigate 
the system, identify attacker activity, and answer forensic questions 
about what happened — simulating a basic DFIR triage from a SOC 
analyst perspective.

---

## Key Artifacts Investigated

### 1. Task Scheduler
Checked scheduled tasks for persistence mechanisms. Attackers commonly 
abuse Task Scheduler to maintain access or execute payloads on startup 
or at timed intervals. Looked for tasks pointing to unusual executables, 
temp directories, or encoded commands.

**SOC relevance:** Scheduled task creation is logged under 
Event ID 4698. Suspicious tasks (e.g., running from AppData or Temp) 
are a common persistence indicator.

### 2. Sysmon Event ID 3 — Network Connections
Used Sysmon logs to correlate processes with outbound network 
connections. Event ID 3 captures the initiating process, source/dest 
IP, and port — useful for identifying C2 beaconing or data exfiltration 
from unexpected processes.

**SOC relevance:** Filtering Event ID 3 by process name (e.g., 
powershell.exe, cmd.exe making outbound connections) is a high-value 
detection technique.

### 3. WebShell
Identified a webshell uploaded to the web server. A webshell is a 
malicious script (e.g., PHP, ASPX) placed on a web server through a 
vulnerable upload function or exploited vulnerability. Once in place, 
the attacker accesses it via browser to execute OS-level commands 
remotely.

**SOC relevance:** Webshells are often detected through web server 
logs (unusual POST requests to static file directories), file integrity 
monitoring, or process trees showing web server processes (w3wp.exe, 
apache2) spawning shells.

### 4. hosts File Manipulation
Found evidence of tampering with `C:\Windows\System32\drivers\etc\hosts`. 
This file is checked before DNS resolution, meaning any entries here 
override DNS entirely. Attackers use this for traffic redirection, 
phishing, and disabling security tool updates by redirecting their 
domains to localhost or a malicious IP.

**SOC relevance:** Changes to the hosts file are a persistence and 
defense evasion technique. File integrity monitoring on this path is 
a recommended detection control.

### 5. Event ID 4672 — Special Privileges Assigned to New Logon
Reviewed 4672 events to identify accounts logging in with sensitive 
privileges (e.g., SeDebugPrivilege, SeTcbPrivilege). This event fires 
at logon for privileged accounts and is used to track admin and 
SYSTEM-level access.

**SOC relevance:** Unexpected 4672 events — especially outside 
business hours or from unusual source IPs — are a strong indicator 
of privilege escalation or lateral movement.

---

## Screenshots

![Screenshot 1](screenshots/RDP.png)
![Screenshot 2](screenshots/Admin_privilages.png)

---

## Takeaways

This room reinforced that Windows forensics relies heavily on 
knowing *where* evidence lives — scheduled tasks, Sysmon logs, 
event IDs, and local config files — before you can analyze it. 
The SOC-relevant skill isn't just finding the artifact, it's 
knowing which event IDs and log sources to query first under 
time pressure.
