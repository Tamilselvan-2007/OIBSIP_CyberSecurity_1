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
| Nmap | 7.x | Network port scanning and service detection |
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
> **Scan Type:** Service Version Detection (`-sV`)  
> **Host Status:** Up

The scan identified the following open ports:

| Port | State | Protocol | Service | Version |
|------|-------|----------|---------|---------|
| 22 | Open | TCP | SSH | OpenSSH 8.x |
| 80 | Open | TCP | HTTP | Apache httpd 2.x |
| 443 | Open | TCP | HTTPS | Apache httpd 2.x (SSL) |
| 3306 | Open | TCP | MySQL | MySQL 8.x |

> **Note:** Actual versions and additional ports may vary based on the system environment. Replace the above with your real scan output.

---

## Port Analysis & Security Significance

### Port 22 — SSH (Secure Shell)

**What it is:** SSH is a cryptographic network protocol used for secure remote login, command execution, and file transfer between machines.

**Why it is open:** A remote access server or administrative tool is running on this machine.

**Security Implications:**
- SSH is a common brute-force target; weak or default passwords are a major risk.
- Leaving SSH open on the default port (22) exposes the service to automated scanning bots.
- **Best practices:** Disable root login, use key-based authentication, consider changing to a non-standard port, and use `fail2ban` to block repeated failed attempts.

---

### Port 80 — HTTP (Hypertext Transfer Protocol)

**What it is:** HTTP is the foundational protocol for data communication on the web. Port 80 indicates a web server is running and serving unencrypted web content.

**Why it is open:** A web server (e.g., Apache, Nginx) is active on this machine.

**Security Implications:**
- HTTP transmits data in **plain text**, making it vulnerable to man-in-the-middle (MITM) attacks and packet sniffing.
- Sensitive login pages or forms running over HTTP expose user credentials.
- **Best practices:** Redirect all HTTP traffic to HTTPS (port 443), implement HTTP Strict Transport Security (HSTS), and keep the web server software updated.

---

### Port 443 — HTTPS (HTTP Secure)

**What it is:** HTTPS is the encrypted version of HTTP, secured using TLS/SSL certificates. It ensures that data between the client and server is encrypted and authenticated.

**Why it is open:** A secure web server is running, serving content over an encrypted channel.

**Security Implications:**
- An outdated or misconfigured TLS certificate can introduce vulnerabilities (e.g., expired certificates, weak cipher suites like SSLv3/TLS 1.0).
- Self-signed certificates can expose users to phishing.
- **Best practices:** Use TLS 1.2 or 1.3 only, disable older SSL/TLS versions, and regularly renew certificates. Use tools like SSL Labs to audit the TLS configuration.

---

### Port 3306 — MySQL (Database Server)

**What it is:** Port 3306 is the default port for MySQL, a widely used relational database management system (RDBMS).

**Why it is open:** A MySQL database server is actively running on this machine.

**Security Implications:**
- An externally exposed MySQL port is a **critical risk** — attackers can attempt to brute-force database credentials directly.
- SQL injection attacks combined with an exposed MySQL port can lead to complete data exfiltration.
- **Best practices:** Bind MySQL to `127.0.0.1` (localhost only) so it is not accessible from outside the machine. Use strong passwords, restrict user privileges to least-privilege, and disable the root account for remote login.

---

## Key Takeaways

1. **Every open port is a potential entry point.** Only necessary services should be running and exposed.
2. **Service version information** (from `-sV`) is critical — outdated versions often have known CVEs.
3. **Default ports** (22, 80, 443, 3306) are actively targeted by automated scanners and bots.
4. **Principle of Least Exposure:** Close or firewall any port that does not need to be publicly accessible.
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
