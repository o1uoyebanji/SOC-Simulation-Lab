# SOC-Simulation-Lab

## Overview
Built a Security Operations Center (SOC) simulation environment to detect and analyze real world cyberattacks using industry standard tools. Configured a multi-VM lab, simulated brute-force and reconnaissance attacks, and built detection logic, alerts, and dashboards in Splunk.

---

## Architecture
Kali Linux -> Ubuntu Target -> Splunk SIEM

- **Kali Linux** — Attacker machine running Hydra and Nmap
- **Ubuntu** — Target victim machine running SSH and Splunk
- **Splunk Enterprise** — SIEM ingesting and analyzing logs

---

## Tools Used
- Splunk Enterprise 10.4.0
- Kali Linux
- Ubuntu 22.04
- Hydra
- Nmap
- VirtualBox

---

## What I Did

### 1. Network Setup
- Configured two VMs on an isolated Host-Only network
- Verified communication between attacker and target machines

### 2. Splunk Configuration
- Installed Splunk Enterprise on the Ubuntu vm
- Installed Splunk Universal Forwarder to also ship logs to Splunk
- Monitored /var/log/auth.log and /var/log/syslog

### 3. Attack Simulation
- **Brute Force SSH** — Used Hydra with rockyou.txt wordlist against Ubuntu SSH, generating 129 failed login attempts
- **Port Scan** — Used Nmap SYN scan (-sS) to enumerate all open ports on the target machine

### 4. Detection in Splunk
Wrote SPL queries to detect attack patterns:

-- Detect failed logins
index=main "Failed password"

-- Aggregate by attacker host
index=main "Failed password" | stats count by host

-- Visualize attack over time
index=main "Failed password" | timechart count span=1m

-- Port scan evidence
index=main port

### 5. Alert Created
- Name: SSH Brute Force Detected
- Condition: Number of results greater than 10
- Severity: High
- Action: Add to Triggered Alerts

### 6. Dashboard Built
- SOC Lab Monitor dashboard with 3 panels:
  - Failed Logins Over Time (line chart)
  - Top Attacking Hosts (bar chart)
  - Total Log Events (single value — 15966)

---

## Key Findings
- Detected 129 failed SSH login attempts from Kali (192.168.100.4)
- Identified attack spike at 3:16 PM using timechart visualization
- Nmap scan showed 5 open ports which were: 22, 8000, 8089, 8191, 9997
- All Splunk ports visible, confirming realistic attack surface

---

## Screenshots
See /screenshots folder for full documentation including:
- Splunk dashboard
- Hydra brute force output
- Nmap scan results
- Detection queries
- Alert configuration
- SOC Lab Monitor dashboard

---

## What I Learned
- How SIEMs ingest and normalize logs from multiple sources
- How to write SPL queries to detect brute force patterns
- How attackers enumerate open ports using Nmap SYN scans
- How to build threshold-based alerting for automated detection
- The importance of log correlation in identifying attack patterns
