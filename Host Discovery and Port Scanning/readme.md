\# Ethical Hacking Lab — Host Discovery \& Port Scanning



\## Overview



This repository contains my hands-on learning and practical notes from \*\*Ethical Hacking\*\* using:



\* \*\*Kali Linux\*\* — Attacker/Testing machine

\* \*\*Metasploitable 2\*\* — Vulnerable target machine

\* \*\*Nmap\*\* — Network discovery and port scanning tool



All practical activities are performed in my own isolated virtual lab environment for educational purposes.



\---



\# 1. Network Interface Information



\## `ifconfig`



\### Command



```bash

ifconfig

```



\### Purpose



Displays network interface information such as:



\* IP address

\* MAC address

\* Netmask

\* Network interface status



\### Important terms



\* `eth0` — Ethernet network interface

\* `inet` — IPv4 address

\* `netmask` — Defines the network range

\* `lo` — Loopback interface

\* `127.0.0.1` — Localhost



\### What I learned



Before performing network scanning, I can use `ifconfig` to identify the IP address of my Kali machine and understand its network configuration.



\---



\# 2. Host Discovery



Host discovery is used to determine whether a host is \*\*alive/reachable\*\* on a network before performing further scanning.



\---



\## 2.1 ICMP Echo Host Discovery



\### Command



```bash

nmap -PE <TARGET-IP>

```



\### Explanation



\* `nmap` — Network Mapper

\* `-PE` — Uses ICMP Echo Request for host discovery

\* `<TARGET-IP>` — IP address of the target



\### Purpose



Checks whether the target responds to an ICMP Echo Request.



\---



\## 2.2 ICMP Echo Ping Scan



\### Command



```bash

nmap -PE -sn <TARGET-IP>

```



\### Explanation



\* `-PE` — ICMP Echo Request

\* `-sn` — Host discovery without performing a port scan



\### Purpose



Used to determine whether a target host is alive without scanning its ports.



\---



\## 2.3 Showing the Reason



\### Command



```bash

nmap -PE -sn <TARGET-IP> --reason

```



\### Explanation



\* `--reason` — Displays the reason Nmap used to determine the host state.



For example, Nmap may report that the host is up because it received an \*\*ICMP Echo Reply\*\*.



\---



\## 2.4 Packet Trace



\### Command



```bash

nmap -PE -sn <TARGET-IP> --reason --packet-trace

```



\### Explanation



\* `--packet-trace` — Displays packets sent and received during the scan.

\* `--reason` — Displays why Nmap determined the host state.



\### Purpose



Useful for understanding what network packets Nmap is actually sending during host discovery.



\---



\## 2.5 Disable ARP Ping



\### Command



```bash

nmap -PE -sn <TARGET-IP> --reason --packet-trace --disable-arp-ping

```



\### Explanation



\* `--disable-arp-ping` — Prevents Nmap from using ARP ping during host discovery.



This can be useful when studying ICMP-based host discovery separately from ARP discovery.



\---



\# 3. Host Discovery Using Non-Echo ICMP



ICMP host discovery does not have to use only Echo Requests. Nmap supports other ICMP message types.



\---



\## 3.1 ICMP Timestamp Discovery



\### Command



```bash

nmap -PP -sn <TARGET-IP>

```



\### Explanation



\* `-PP` — Uses an ICMP Timestamp Request

\* `-sn` — Host discovery without port scanning



\### Purpose



Uses ICMP Timestamp requests to determine whether a host is reachable.



\---



\## 3.2 ICMP Address Mask Discovery



\### Command



```bash

nmap -PM -sn <TARGET-IP>

```



\### Explanation



\* `-PM` — Uses an ICMP Address Mask Request

\* `-sn` — Host discovery without port scanning



\### Purpose



Another ICMP-based host discovery technique.



\---



\# 4. Host Discovery Using TCP Sweep



TCP-based host discovery sends TCP probes to determine whether hosts are reachable.



\---



\## 4.1 TCP SYN Discovery



\### Command



```bash

nmap -sn -PS80 <TARGET-IP>

```



\### Explanation



\* `-sn` — Host discovery without port scanning

\* `-PS80` — Sends a TCP SYN probe to port 80

\* `80` — HTTP port



\### Purpose



Used to determine whether a host is reachable using a TCP SYN probe.



\### Important



`-PS` is used for \*\*TCP SYN host discovery\*\*.



\---



\## 4.2 TCP ACK Discovery



\### Command



```bash

nmap -sn -PA80 <TARGET-IP>

```



\### Explanation



\* `-PA80` — Sends a TCP ACK probe to port 80

\* `-sn` — Host discovery



\### Purpose



Uses TCP ACK packets as a host discovery method.



\---



\# 5. Host Discovery Using UDP Sweep



UDP discovery uses UDP probes to determine whether hosts are reachable.



\## UDP Discovery



\### Command



```bash

nmap -sn -PU53 <TARGET-IP>

```



\### Explanation



\* `-PU` — UDP host discovery

\* `53` — DNS port

\* `-sn` — Host discovery without port scanning



\### Purpose



Sends a UDP probe to the specified port.



A response can indicate that the host is reachable. An ICMP Port Unreachable response can also provide evidence that the host exists.



\---



\# 6. TCP Port Scanning



Port scanning is used to identify the state of TCP ports on a target.



Common states include:



\* Open

\* Closed

\* Filtered



\---



\# 7. TCP Connect Scan



\## `-sT`



\### Command



```bash

nmap -sT <TARGET-IP>

```



\### Purpose



Performs a TCP Connect scan by establishing a complete TCP connection with the target port.



\### TCP Three-Way Handshake



```text

SYN

\&#x20;  ↓

SYN/ACK

\&#x20;  ↓

ACK

```



If the connection can be established, the port is considered open.



\### What I learned



TCP Connect scanning completes the TCP connection and is generally more detectable than a SYN scan.



\---



\# 8. TCP SYN Scan



\## `-sS`



\### Command



```bash

sudo nmap -sS <TARGET-IP>

```



\### Purpose



Performs a TCP SYN scan to identify open TCP ports without completing the normal TCP three-way handshake.



\### For an Open Port



```text

Kali                    Target

\&#x20; │

\&#x20; │──── SYN ──────────►

\&#x20; │◄── SYN/ACK ────────

\&#x20; │──── RST ──────────►

```



A SYN/ACK response indicates that the port is open.



\### What I learned



`-sS` is commonly called a \*\*TCP SYN scan\*\*, \*\*half-open scan\*\*, or \*\*TCP stealth scan\*\*.



\---



\# 9. TCP Stealth Scan



TCP Stealth Scan generally refers to the \*\*TCP SYN Scan\*\* in Nmap.



\### Command



```bash

sudo nmap -sS <TARGET-IP>

```



\### Key concept



The TCP connection is not completed.



```text

SYN → SYN/ACK → RST

```



Instead of:



```text

SYN → SYN/ACK → ACK

```



\### Important



Stealth does not mean completely invisible. Modern security monitoring and IDS/IPS systems can detect SYN scanning activity.



\---



\# 10. FTP Bounce Scan



FTP Bounce scanning uses an FTP server as an intermediary to perform a scan toward another target.



\### Basic syntax



```bash

nmap -b <FTP-SERVER> <TARGET-IP>

```



\### Example



```bash

nmap -b 192.168.56.101 192.168.56.102

```



In this example:



\* `-b` — FTP bounce scan

\* `192.168.56.101` — FTP server/bounce host

\* `192.168.56.102` — Target being scanned



\### Concept



```text

Kali

\&#x20; │

\&#x20; │ FTP

\&#x20; ▼

FTP Server

\&#x20; │

\&#x20; │ Scan/probe

\&#x20; ▼

Target

```



The FTP server acts as an intermediary.



\---



\## FTP Bounce Scan with Verbose Mode



\### Command



```bash

nmap -v -b <FTP-SERVER> <TARGET-IP>

```



\### Explanation



\* `-v` — Verbose mode

\* `-b` — FTP bounce scan



Verbose mode displays additional information while Nmap performs the scan.



\---



\## FTP Bounce Scan with Authentication



\### Command



```bash

nmap -v -b <USERNAME>:<PASSWORD>@<FTP-SERVER> <TARGET-IP>

```



\### Explanation



\* `<USERNAME>` — FTP username

\* `<PASSWORD>` — FTP password

\* `@` — Separates credentials from the FTP server

\* `<FTP-SERVER>` — FTP server used for the bounce

\* `<TARGET-IP>` — Target being scanned

\* `-v` — Verbose output



\### What I learned



The FTP server is used as an intermediary instead of Kali directly connecting to the target.



FTP bounce scanning is mainly important today as a security concept because modern FTP servers generally restrict this behavior.



\---



\# 11. Important Nmap Options Learned



| Option               | Purpose                                  |

| -------------------- | ---------------------------------------- |

| `-PE`                | ICMP Echo Request discovery              |

| `-PP`                | ICMP Timestamp discovery                 |

| `-PM`                | ICMP Address Mask discovery              |

| `-PS`                | TCP SYN host discovery                   |

| `-PA`                | TCP ACK host discovery                   |

| `-PU`                | UDP host discovery                       |

| `-sn`                | Host discovery without port scanning     |

| `--reason`           | Shows why Nmap determined the host state |

| `--packet-trace`     | Shows packets sent and received          |

| `--disable-arp-ping` | Disables ARP ping                        |

| `-sT`                | TCP Connect Scan                         |

| `-sS`                | TCP SYN/Stealth Scan                     |

| `-b`                 | FTP Bounce Scan                          |

| `-v`                 | Verbose output                           |



\---



\# 12. Host Discovery vs Port Scanning



One important concept I learned is the difference between \*\*host discovery\*\* and \*\*port scanning\*\*.



\### Host Discovery



Question:



> Is the machine alive/reachable?



Examples:



```bash

nmap -sn -PE <TARGET-IP>

```



```bash

nmap -sn -PS80 <TARGET-IP>

```



```bash

nmap -sn -PU53 <TARGET-IP>

```



\### Port Scanning



Question:



> Which ports are open on the machine?



Examples:



```bash

nmap -sT <TARGET-IP>

```



```bash

sudo nmap -sS <TARGET-IP>

```



\---



\# 13. Lab Environment



My practical environment:



```text

┌──────────────────────┐

│      Kali Linux      │

│   Attacker / Tester  │

└──────────┬───────────┘

\&#x20;          │

\&#x20;          │ Isolated Lab Network

\&#x20;          │

┌──────────▼───────────┐

│    Metasploitable 2  │

│   Vulnerable Target  │

└──────────────────────┘

```



All commands and techniques documented here are practiced in my own virtual lab environment for educational and ethical hacking purposes.



\---



\# Key Takeaways



1\. `ifconfig` helps identify network interface and IP information.

2\. Host discovery determines whether a system is reachable.

3\. `-PE` uses ICMP Echo Request.

4\. `-PP` uses ICMP Timestamp Request.

5\. `-PM` uses ICMP Address Mask Request.

6\. `-PS` uses TCP SYN probes for host discovery.

7\. `-PA` uses TCP ACK probes for host discovery.

8\. `-PU` uses UDP probes for host discovery.

9\. `-sT` performs a TCP Connect scan.

10\. `-sS` performs a TCP SYN/Stealth scan.

11\. `-b` performs an FTP Bounce scan.

12\. `--reason` explains Nmap's host-state decision.

13\. `--packet-trace` helps understand the actual packets involved.

14\. Host discovery and port scanning are different stages of network reconnaissance.



\---



\## Disclaimer



These notes are created for educational purposes. All practical testing documented in this repository is performed against intentionally vulnerable machines in an isolated lab environment that I control.

# 

