# Ethical Hacking — NPTEL Hands-on Learning

A practical cybersecurity repository documenting my **hands-on learning and lab work** throughout the **NPTEL Ethical Hacking** course.

> **Lab Notice:** All practical activities documented here are performed in an authorized, isolated lab environment for educational purposes.

## 🎯 Objectives

- Apply ethical hacking concepts through hands-on practice.
- Build practical familiarity with common cybersecurity tools.
- Understand reconnaissance, vulnerability scanning, password attacks, and system-hacking techniques.
- Document commands, observations, and lab results clearly.

## 🧪 Lab Environment

| Component | Role |
|---|---|
| **Kali Linux** | Attacker / Testing Machine |
| **Metasploitable 2** | Intentionally Vulnerable Target |
| **VMware Virtual Network** | Isolated Lab Network |

Target used in the current lab: `192.168.37.130`

## 🛠️ Tools & Technologies

- **Nmap** — Network discovery and port scanning
- **Nmap NSE** — Security and vulnerability scanning
- **Crunch** — Wordlist generation
- **Hydra** — Online password attacks in the lab
- **Telnet** — Remote-login demonstration
- **SSH** — Secure remote access and legacy SSH troubleshooting
- **Metasploit Framework** — Exploitation framework used in the lab
- **Wireshark** — Packet analysis and network-security study

## 📚 Practical Work

### 1. Host Discovery & Port Scanning

Documented techniques include:

- ICMP Echo, Timestamp, and Address Mask discovery
- TCP SYN, TCP ACK, and UDP host discovery
- TCP Connect scanning
- TCP SYN / half-open scanning
- FTP Bounce scanning
- Nmap `--reason` and `--packet-trace` analysis

📁 [Host Discovery and Port Scanning/](./Host%20Discovery%20and%20Port%20Scanning/)

### 2. Vulnerability Scanning

Documented work includes:

- Full TCP port scanning
- Service and OS discovery
- SMB OS discovery
- NSE script usage and troubleshooting
- VSFTPD backdoor detection
- CVE-2011-2523
- SMB and SSH brute-force scripts
- HTTP Slowloris vulnerability checking

📁 [Vulnerability Scanning/](./Vulnerability%20Scanning/)

### 3. Password Cracking & Remote Login

Documented work includes:

- Telnet remote login
- SSH remote login and legacy SSH troubleshooting
- Crunch wordlist generation
- Custom character sets and permutations
- Saving generated wordlists
- Hydra SSH, FTP, and Telnet attacks

📁 [System Hacking - Password Cracking/](./System%20Hacking%20-%20Password%20Cracking/)

## 🔬 Documentation Style

Each practical focuses on the objective, lab environment, commands used, command explanations, observed results, screenshots, and key takeaways.

## 🗂️ Repository Structure

```text
Ethical-Hacking/
│
├── Readme.md
├── Host Discovery and Port Scanning/
│   ├── readme.md
│   └── Screenshots/
├── Vulnerability Scanning/
│   ├── vulnerability scanning.md
│   └── Screenshots/
└── System Hacking - Password Cracking/
    ├── System Hacking -password Cracking.md
    └── Screenshots/
```

The repository will continue to grow as additional NPTEL Ethical Hacking practical topics are completed.

## ⚠️ Responsible Use

The techniques documented here are intended for personal labs, intentionally vulnerable machines, authorized security testing, and educational purposes.

**Do not test systems or networks without explicit authorization.**

## 📈 Learning Progress

This is a work in progress. New practical exercises, demonstrations, screenshots, and documentation will be added as the course progresses.

## 👨‍💻 About

This repository represents my practical learning journey in **ethical hacking and cybersecurity**, with an emphasis on learning by doing and documenting controlled security experiments.

**Course:** NPTEL Ethical Hacking  
**Platform:** NPTEL  
**Focus:** Ethical Hacking • Penetration Testing • Network Security • Vulnerability Assessment

---

⭐ Personal hands-on cybersecurity learning record.