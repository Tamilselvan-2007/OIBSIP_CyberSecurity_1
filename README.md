# Task 1: Basic Network Scanning with Nmap

![Internship](https://img.shields.io/badge/Internship-Oasis%20Infobyte-blue)
![Tool](https://img.shields.io/badge/Tool-Nmap-green)
![Level](https://img.shields.io/badge/Level-Beginner-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

## Overview

This task demonstrates basic network scanning using **Nmap (Network Mapper)** to identify open ports and running services on a local machine. Network scanning is a foundational skill in cybersecurity, used during penetration testing and vulnerability assessments to map the attack surface of a system.

---

## Objective

Perform a network scan on a local machine to:
- Identify all open ports
- Detect services and their versions running on those ports
- Document findings and explain the security significance of each port

---

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Nmap | 7.99 | Network port scanning and service detection |
| Terminal / CMD | — | Executing scan commands |

---

## Methodology

### Step 1 — Basic Port Scan

```bash
nmap 127.0.0.1
```

Scans the top 1000 most commonly used ports on localhost (our own machine).

### Step 2 — Service Version Detection

```bash
nmap -sV 127.0.0.1
```

The `-sV` flag probes open ports to detect the exact software and version running on each service.

### Step 3 — Aggressive Scan (OS + Scripts)

```bash
nmap -A 127.0.0.1
```

Enables OS detection, version detection, and default NSE script scanning for deeper enumeration.

### Step 4 — Save Results to File

```bash
nmap -sV 127.0.0.1 -oN nmap_scan_results.txt
```

Saves all output in normal format to `nmap_scan_results.txt` for documentation and reporting.

---

## Scan Results

> **Target:** `127.0.0.1` (localhost)  
> **Scan Type:** Basic scan + Service Version Detection (`-sV`)  
> **Nmap Version:** 7.99  
> **Host Status:** Up (0.00038s latency)  
> **OS Detected:** Windows (CPE: `cpe:/o:microsoft:windows`)  
> **Scan Date:** 2026-06-09  
> **Closed Ports:** 997 (reset)

The scan identified the following **3 open ports** out of 1000 scanned:

| Port | State | Protocol | Service | Version |
|------|-------|----------|---------|---------|
| 135 | Open | TCP | msrpc | Microsoft Windows RPC |
| 445 | Open | TCP | microsoft-ds | Windows SMB (version undetected) |
| 3306 | Open | TCP | mysql | MySQL 8.0.41 |

---

## Port Analysis & Security Significance

### Port 135 — MSRPC (Microsoft Remote Procedure Call)

**What it is:** MSRPC is a Windows protocol that allows programs to request services from other programs on the network. It acts as an endpoint mapper — when a client wants to communicate with an RPC service, it first contacts port 135 to find out which dynamic port the service is listening on.

**Why it is open:** This is a default Windows system service that starts automatically. It supports core Windows features like DCOM (Distributed Component Object Model), WMI (Windows Management Instrumentation), and remote service management.

**Security Implications:**
- Port 135 has historically been targeted by major worms such as **Blaster (MS03-026)** and **Conficker**, which exploited RPC vulnerabilities for remote code execution.
- Exposing port 135 on a network allows attackers to enumerate RPC endpoints and identify exploitable services.
- **Best practices:** Block port 135 at the firewall for all external and untrusted interfaces. On internal networks, apply the latest Windows security patches. Disable unnecessary DCOM services via `dcomcnfg`.

---

### Port 445 — Microsoft-DS (SMB — Server Message Block)

**What it is:** Port 445 is used by Windows SMB (Server Message Block) protocol for file sharing, printer sharing, and inter-process communication over a network. It is the modern replacement for the older NetBIOS-based file sharing (ports 137–139).

**Why it is open:** Windows enables SMB by default for network file and printer sharing. Even on standalone machines, SMB is active for local inter-process communication and administrative shares.

**Security Implications:**
- Port 445 was the primary attack vector for the **WannaCry ransomware (2017)** and **NotPetya**, which used the EternalBlue exploit (MS17-010) against unpatched SMB implementations.
- Exposed SMB allows **credential relay attacks** (NTLM relay), **pass-the-hash** attacks, and **brute-force of Windows credentials**.
- SMB version 1 (SMBv1) is critically insecure and should never be used.
- **Best practices:** Block port 445 at the perimeter firewall — it should never be exposed to the internet. Disable SMBv1 via PowerShell: `Set-SmbServerConfiguration -EnableSMB1Protocol $false`. Enable SMB signing to prevent relay attacks.

---

### Port 3306 — MySQL 8.0.41 (Database Server)

**What it is:** Port 3306 is the default port for MySQL, a widely used open-source relational database management system (RDBMS). The scan confirmed **MySQL version 8.0.41** is running on this machine.

**Why it is open:** MySQL is installed and running — likely supporting a local web application, development environment, or backend service.

**Security Implications:**
- An externally accessible MySQL port is a **critical risk** — attackers can directly brute-force database credentials or exploit version-specific CVEs.
- MySQL 8.0.41 should be checked against the [CVE database](https://cve.mitre.org/) for any known vulnerabilities in that specific version.
- Exposing the database port means SQL injection vulnerabilities in the application could give attackers direct database access.
- **Best practices:** Bind MySQL strictly to localhost by setting `bind-address = 127.0.0.1` in `my.ini`. Use strong, unique passwords for all database users. Restrict user privileges to only what is needed (principle of least privilege). Disable remote root login. Keep MySQL updated to the latest patch release.

---

## Key Takeaways

1. **Every open port is a potential entry point.** Only necessary services should be running and exposed.
2. **Port 445 (SMB) is a high-risk port** — it was the attack vector for WannaCry and NotPetya; it must be firewalled from external networks.
3. **Port 135 (MSRPC)** has a history of critical RPC exploits and should never be exposed beyond trusted internal networks.
4. **Service version information** (from `-sV`) is critical — MySQL 8.0.41 can be cross-referenced against CVE databases to check for known vulnerabilities.
5. **Network scanning is the first step** in any penetration test or security audit — knowing what is exposed is fundamental to defending it.

---

## Files in This Repository

```
Task1-Nmap-Network-Scanning/
│
├── README.md                  ← This file (documentation and analysis)
├── nmap_scan_results.txt      ← Raw Nmap scan output (-oN format)
└── screenshots/
    ├── nmap_basic_scan.png    ← Screenshot of basic nmap 127.0.0.1 output
    └── nmap_service_scan.png  ← Screenshot of nmap -sV output
```

---

## Learning Outcome

Through this task, I gained hands-on experience with:
- Installing and using Nmap for network reconnaissance
- Interpreting port scan results and identifying running services
- Understanding the security implications of commonly open ports
- Documenting cybersecurity findings in a structured format

---

## References

- [Nmap Official Documentation](https://nmap.org/docs.html)
- [SANS Institute — Nmap Cheat Sheet](https://www.sans.org/security-resources/sec560/netcat_cheat_sheet_v1.pdf)
- [OWASP — Network Security Testing](https://owasp.org/www-project-web-security-testing-guide/)
- [CVE Database — MITRE](https://cve.mitre.org/)

---

*This task is part of the **Security Analyst Internship** at **Oasis Infobyte** under the Structured Internship Program (SIP).*
