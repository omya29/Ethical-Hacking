# Host Discovery & Port Scanning using Nmap

Nmap (Network Mapper) is a free and open-source utility for network discovery and security auditing.

This practical focuses on **host discovery** and **port scanning** using Nmap in a controlled lab environment.

## 🧪 Requirements

- Kali Linux virtual machine — Attacker/Testing machine
- Metasploitable 2 virtual machine — Vulnerable target machine
- Both machines configured on the same isolated virtual network (for example, NAT or Host-Only)

## 🎯 Objectives

- Identify network interface information.
- Understand the difference between Host Discovery and Port Scanning.
- Identify active hosts using ICMP Echo, Timestamp, and Address Mask requests.
- Identify active hosts using TCP SYN, TCP ACK, and UDP sweeps.
- Understand TCP Connect, TCP SYN (Stealth), and FTP Bounce scanning techniques.
- Analyze packet traces and reasons for host states.

---

## Nmap Basics

Nmap is installed by default in Kali Linux. Before performing network scanning, it is essential to understand the network configuration and the different discovery methodologies available.

### 1. Network Interface Information

**Command:**

```bash
ifconfig
```

**Purpose:**  
Displays network interface information such as IP address, MAC address, netmask, and network interface status.

Important terms include:

- `eth0` — Ethernet interface
- `inet` — IPv4 address
- `lo` — Loopback interface

---

# Host Discovery

Host discovery is used to determine whether a host is alive/reachable on a network before performing further scanning.

## 2. ICMP Echo Host Discovery

**Command:**

```bash
nmap -PE <TARGET-IP>
```

**Purpose:**  
Uses ICMP Echo Request for host discovery to check whether the target responds.

### Host Discovery Without Port Scanning

```bash
nmap -PE -sn <TARGET-IP>
```

**Purpose:**  
`-sn` performs host discovery without performing a port scan.

### Show the Reason for the Host State

```bash
nmap -PE -sn <TARGET-IP> --reason
```

**Purpose:**  
`--reason` displays the reason Nmap used to determine the host state.

### Display Packets

```bash
nmap -PE -sn <TARGET-IP> --reason --packet-trace
```

**Purpose:**  
`--packet-trace` displays packets sent and received during the scan.

### Disable ARP Ping

```bash
nmap -PE -sn <TARGET-IP> --reason --packet-trace --disable-arp-ping
```

**Purpose:**  
`--disable-arp-ping` prevents Nmap from using ARP ping during host discovery.

---

## 3. Host Discovery Using Non-Echo ICMP

ICMP host discovery does not have to use only Echo Requests. Nmap supports other ICMP message types.

### ICMP Timestamp

```bash
nmap -PP -sn <TARGET-IP>
```

**Purpose:**  
`-PP` uses an ICMP Timestamp Request to determine whether a host is reachable.

### ICMP Address Mask

```bash
nmap -PM -sn <TARGET-IP>
```

**Purpose:**  
`-PM` uses an ICMP Address Mask Request as another ICMP-based host discovery technique.

---

## 4. Host Discovery Using TCP Sweep

TCP-based host discovery sends TCP probes to determine whether hosts are reachable.

### TCP SYN Probe

```bash
nmap -sn -PS80 <TARGET-IP>
```

**Purpose:**  
`-PS80` sends a TCP SYN probe to port 80.

### TCP ACK Probe

```bash
nmap -sn -PA80 <TARGET-IP>
```

**Purpose:**  
`-PA80` sends a TCP ACK probe to port 80.

---

## 5. Host Discovery Using UDP Sweep

UDP discovery uses UDP probes to determine whether hosts are reachable.

```bash
nmap -sn -PU53 <TARGET-IP>
```

**Purpose:**  
`-PU` sends a UDP probe to the specified port. Here, port 53 is used.

A response can indicate that the host is reachable. An ICMP Port Unreachable response can also provide evidence that the host exists.

---

# TCP Port Scanning

Port scanning is used to identify the state of TCP ports on a target.

Common states include:

- **Open**
- **Closed**
- **Filtered**

## 6. TCP Connect Scan

**Command:**

```bash
nmap -sT <TARGET-IP>
```

**Purpose:**  
Performs a TCP Connect scan by establishing a complete TCP connection with the target port.

### TCP Three-Way Handshake

```text
SYN
 ↓
SYN/ACK
 ↓
ACK
```

If the connection can be established, the port is considered open.

TCP Connect scanning completes the TCP connection and is generally more detectable than a SYN scan.

---

## 7. TCP SYN Scan (Stealth Scan)

**Command:**

```bash
sudo nmap -sS <TARGET-IP>
```

**Purpose:**  
Performs a TCP SYN scan to identify open TCP ports without completing the normal TCP three-way handshake.

### Open Port

```text
Kali                    Target
  │
  │──── SYN ──────────►
  │◄── SYN/ACK ────────
  │──── RST ──────────►
```

A SYN/ACK response indicates that the port is open.

`-sS` is commonly called a TCP SYN scan, half-open scan, or TCP stealth scan.

> **Note:** Stealth does not mean completely invisible. Modern security monitoring and IDS/IPS systems can detect SYN scanning activity.

---

## 8. FTP Bounce Scan

FTP Bounce scanning uses an FTP server as an intermediary to perform a scan toward another target.

**Command:**

```bash
nmap -b <FTP-SERVER> <TARGET-IP>
```

**Example:**

```bash
nmap -b 192.168.56.101 192.168.56.102
```

### Concept

```text
Kali
  │
  │ FTP
  ▼
FTP Server
  │
  │ Scan/probe
  ▼
Target
```

The FTP server acts as an intermediary.

FTP bounce scanning is mainly important today as a security concept because modern FTP servers generally restrict this behavior.

### Verbose Mode

```bash
nmap -v -b <FTP-SERVER> <TARGET-IP>
```

**Purpose:**  
`-v` enables verbose mode and displays additional information while Nmap performs the scan.

### FTP Authentication

```bash
nmap -v -b <USERNAME>:<PASSWORD>@<FTP-SERVER> <TARGET-IP>
```

**Purpose:**  
Allows authentication to the FTP server used for the bounce.

The `@` separates credentials from the FTP server.

---

# Important Nmap Options Learned

| Option | Purpose |
|---|---|
| `-PE` | ICMP Echo Request discovery |
| `-PP` | ICMP Timestamp discovery |
| `-PM` | ICMP Address Mask discovery |
| `-PS` | TCP SYN host discovery |
| `-PA` | TCP ACK host discovery |
| `-PU` | UDP host discovery |
| `-sn` | Host discovery without port scanning |
| `--reason` | Shows why Nmap determined the host state |
| `--packet-trace` | Shows packets sent and received |
| `--disable-arp-ping` | Disables ARP ping |
| `-sT` | TCP Connect Scan |
| `-sS` | TCP SYN/Stealth Scan |
| `-b` | FTP Bounce Scan |
| `-v` | Verbose output |

---

# Host Discovery vs Port Scanning

### Host Discovery

**Question:** Is the machine alive/reachable?

```bash
nmap -sn -PE <TARGET-IP>
nmap -sn -PS80 <TARGET-IP>
nmap -sn -PU53 <TARGET-IP>
```

### Port Scanning

**Question:** Which ports are open on the machine?

```bash
nmap -sT <TARGET-IP>
sudo nmap -sS <TARGET-IP>
```

---

# Lab Environment

```text
┌──────────────────────┐
│      Kali Linux      │
│   Attacker / Tester  │
└──────────┬───────────┘
           │
           │ Isolated Lab Network
           │
┌──────────▼───────────┐
│    Metasploitable 2  │
│   Vulnerable Target  │
└──────────────────────┘
```

---

# Key Takeaways

- `ifconfig` helps identify network interface and IP information.
- Host discovery determines whether a system is reachable.
- `-PE` uses ICMP Echo Request.
- `-PP` uses ICMP Timestamp Request.
- `-PM` uses ICMP Address Mask Request.
- `-PS` uses TCP SYN probes for host discovery.
- `-PA` uses TCP ACK probes for host discovery.
- `-PU` uses UDP probes for host discovery.
- `-sT` performs a TCP Connect scan.
- `-sS` performs a TCP SYN/Stealth scan.
- `-b` performs an FTP Bounce scan.
- `--reason` explains Nmap's host-state decision.
- `--packet-trace` helps understand the actual packets involved.
- Host discovery and port scanning are different stages of network reconnaissance.
