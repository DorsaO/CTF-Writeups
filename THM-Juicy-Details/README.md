## Scenario
A juice shop web server was compromised. Given an Apache access log,
the task was to identify the attacker's tools, techniques, and 
what data was stolen.

---

## Attack Summary

| Phase | Tool | Technique |
|---|---|---|
| Reconnaissance | Nmap NSE | Service/path scanning |
| Enumeration | Firefox (Kali) | Manual API browsing |
| SQL Injection | sqlmap 1.5.2 | Automated injection testing |
| Exfiltration | curl | UNION-based dump of Users table |
| Dir Brute-force | feroxbuster | Discovered /ftp, /admin, /backup |

**Attacker IP:** `192.168.10.5` → **Target:** `192.168.10.4`  
**Data stolen:** User IDs, emails, and password hashes via 
UNION SELECT from the `Users` table.

---

## Key Takeaway
All tools used (Nmap, sqlmap, feroxbuster) advertise themselves 
in the user-agent by default — making detection straightforward 
from access logs alone. Response size spikes are a reliable 
exfiltration indicator.

---

## Screenshot
![Log Analysis](screenshots/Stolen_data.png)
