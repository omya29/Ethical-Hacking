# Metasploitable 2 — Enumeration & Privilege Escalation

**Date:** September 25, 2026  
**Attacker:** Kali Linux  
**Target:** Metasploitable 2 — `192.168.37.130`  
**Lab:** Authorized, isolated virtual environment

---

## Objective

This practical documents enumeration of the Metasploitable 2 machine and the transition from an available user account to administrative privileges.

The work covers:

- Nmap reconnaissance
- SMB/RPC enumeration
- FTP access and file handling
- SSH access
- Sudo privilege checking
- Privilege escalation through sudo permissions

---

## Lab Environment

| Machine | Role |
|---|---|
| Kali Linux | Attacker / testing machine |
| Metasploitable 2 | Intentionally vulnerable target |
| VMware virtual network | Isolated lab network |

**Target IP:** `192.168.37.130`

> These techniques are documented for the intentionally vulnerable Metasploitable 2 lab only.

---

# Phase 1 — Initial Reconnaissance

The first step was to identify open ports and running services on the target.

### Command

```bash
nmap -sS -sV -O 192.168.37.130
```

### Options

| Option | Meaning |
|---|---|
| `-sS` | SYN Stealth Scan |
| `-sV` | Service/version detection |
| `-O` | OS detection |

### Observed Services

The reconnaissance identified services including:

- FTP — port 21
- SSH — port 22
- Telnet — port 23
- HTTP — port 80
- SMB — ports 139/445
- MySQL — port 3306
- Tomcat — port 8180

---

# Phase 2 — SMB & RPC Enumeration

After identifying SMB services, enumeration was performed to obtain information about the target.

## 2.1 Enum4linux

### Command

```bash
enum4linux 192.168.37.130
```

### Concepts

**SMB Null Session:** A connection to SMB using no username/password where the service permits information queries.

**RID Cycling:** Enumerating users by querying Relative Identifiers (RIDs).

### Observed Information

The enumeration output included:

- Workgroup/domain information
- Hostname: `METASPLOITABLE`
- User/account information
- Password-policy information

---

## 2.2 SMB Share Enumeration

### Command

```bash
smbclient -L 192.168.37.130
```

### Purpose

Lists available SMB shares exposed by the target.

---

## 2.3 RPC Null Session

### Command

```bash
rpcclient -U "" 192.168.37.130
```

The empty username is used for a null-session connection where the target permits it.

### Commands Used Inside rpcclient

```text
querydominfo
enumdomusers
queryuser msfadmin
exit
```

### Purpose

| Command | Purpose |
|---|---|
| `querydominfo` | Query domain information |
| `enumdomusers` | Enumerate users |
| `queryuser msfadmin` | Query information for the msfadmin account |
| `exit` | Leave rpcclient |

---

# Phase 3 — FTP Access & File Handling

Credentials available in the lab were used to access the FTP service.

### Connection

```bash
ftp 192.168.37.130
```

**Lab credentials:** `msfadmin / msfadmin`

### FTP Commands

```text
ls -la
put pass.txt
ls
get pass.txt
exit
```

### What Was Demonstrated

- Listing files with `ls -la`
- Uploading a file with `put`
- Verifying the uploaded file
- Downloading a file with `get`
- Closing the FTP session

This demonstrates basic file handling through an authenticated FTP session.

---

# Phase 4 — SSH Access

Metasploitable 2 uses an older SSH configuration that requires a legacy host-key algorithm to be explicitly enabled by the modern Kali SSH client.

### Initial Command

```bash
ssh msfadmin@192.168.37.130
```

The connection returned:

```text
Unable to negotiate with 192.168.37.130 port 22:
no matching host key type found.
Their offer: ssh-rsa,ssh-dss
```

### Lab Workaround

```bash
ssh -o HostKeyAlgorithms=+ssh-rsa msfadmin@192.168.37.130
```

### Option Breakdown

| Part | Meaning |
|---|---|
| `ssh` | Start an SSH connection |
| `-o` | Set an SSH configuration option |
| `HostKeyAlgorithms=+ssh-rsa` | Permit the legacy RSA host-key algorithm for this connection |
| `msfadmin@192.168.37.130` | Connect as msfadmin to the target |

> This option is used only for the isolated Metasploitable 2 lab. It does not modify the global SSH configuration.

---

# Phase 5 — Privilege Escalation

According to the NPTEL concept, an attacker may first obtain access using a **non-admin user account** and then attempt to gain administrative privileges.

In this lab, the `msfadmin` account was checked for sudo permissions.

## Check Sudo Permissions

### Command

```bash
sudo -l
```

### Purpose

`sudo -l` lists the commands that the current user is permitted to execute with sudo privileges.

The lab result showed that `msfadmin` had permission to run commands with full sudo privileges.

## Obtain Root Shell

### Command

```bash
sudo su
```

After authentication with the lab account password:

```text
msfadmin
```

The resulting identity was checked:

```bash
whoami
```

### Observed Result

```text
root
```

This demonstrates **privilege escalation** from the `msfadmin` user account to the `root` account through the available sudo permissions.

---

# NPTEL Concept — Privilege Escalation

### Vertical Privilege Escalation

Gaining **higher privileges** than the privileges already held.

**Example from this lab:**

```text
msfadmin
    ↓
sudo permissions
    ↓
root
```

### Horizontal Privilege Escalation

Acquiring the **same level of privileges** but assuming the identity of another user with similar privileges.

---

## Defenses Against Privilege Escalation

The NPTEL material lists these defenses:

- Restrict interactive logon privileges.
- Use encryption to protect sensitive data.
- Run user-level applications with the least privileges.
- Reduce the amount of code that runs with particular privilege.

---

# Screenshots

The practical evidence is stored in this folder:

- [Screenshot 331](./Screenshot%20%28331%29.png)
- [Screenshot 332](./Screenshot%20%28332%29.png)
- [Screenshot 333](./Screenshot%20%28333%29.png)
- [Screenshot 334](./Screenshot%20%28334%29.png)
- [Screenshot 335](./Screenshot%20%28335%29.png)
- [Screenshot 336](./Screenshot%20%28336%29.png)
- [Screenshot 337](./Screenshot%20%28337%29.png)
- [Screenshot 338](./Screenshot%20%28338%29.png)
- [Screenshot 339](./Screenshot%20%28339%29.png)
- [Screenshot 340](./Screenshot%20%28340%29.png)
- [Screenshot 341](./Screenshot%20%28341%29.png)
- [Screenshot 342](./Screenshot%20%28342%29.png)
- [Screenshot 343](./Screenshot%20%28343%29.png)

---

# Key Takeaways

1. **Nmap** was used for initial reconnaissance and service discovery.
2. **Enum4linux, smbclient, and rpcclient** were used for SMB/RPC enumeration.
3. **FTP** was used for authenticated file handling.
4. **SSH** required a legacy `ssh-rsa` host-key option for this old target.
5. **sudo -l** was used to inspect the privileges available to `msfadmin`.
6. **sudo su** followed by `whoami` demonstrated the transition to the `root` account.
7. The privilege escalation demonstrated here is **vertical privilege escalation**.

---

## Responsible Use

This walkthrough is intended only for the intentionally vulnerable Metasploitable 2 machine in an authorized lab environment.

**Do not test these techniques against systems or networks without explicit authorization.**
