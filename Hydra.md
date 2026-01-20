
**Hydra** is a **network login brute-force tool** used to test authentication strength across many services (FTP, SSH, SMB, RDP, etc.).

It works by:
1. Trying username(s)
2. Trying password(s)
3. Checking if authentication succeeds

## Core Hydra syntax (master template)

```bash
hydra [options] <target> <service>
```

service list replacements
- rdp
- ssh
- telnet
- ftp
- smb
- snmp
- mysql
- mssql
- pop3
- vnc
- ldap2
### Username options

- `-l USER` → single username
- `-L userlist.txt` → multiple usernames

### Password options

- `-p PASS` → single password
- `-P passlist.txt` → multiple passwords

### Common flags

|Flag|Meaning|
|---|---|
|`-t`|Number of parallel threads|
|`-f`|Stop after first success|
|`-V`|Verbose (show every attempt)|


## FTP brute force

### Clean syntax

```bash
hydra -l root -P passwords.txt -t 32 <IP> ftp
```

### Explanation

- `-l root` → target username
- `-P passwords.txt` → password list
- `-t 32` → 32 parallel login attempts (fast)
- `ftp` → FTP service (port 21)

🧠 **Used when**: FTP allows password authentication.

---

## MySQL brute force

### Clean syntax

```bash
hydra -L usernames.txt -P pass.txt <IP> mysql
```

### Explanation

- Multiple usernames
- Multiple passwords
- Tests MySQL login (default port 3306)

🧠 **Used when**: MySQL is exposed to the network.

---

## POP3 brute force

### Clean syntax

```bash
hydra -l USERNAME -P /path/to/passwords.txt -f -V <IP> pop3
```

### Explanation

- `-f` → stop on first valid login
- `-V` → show each attempt (useful for learning/debugging)

🧠 **Used when**: Mail servers expose POP3 (port 110).

---

## RDP brute force

### Clean syntax

```bash
hydra -V -f -L userslist.txt -P passwlist.txt rdp://<IP>
```

### Explanation

- RDP requires **URL-style syntax**
- Targets Windows Remote Desktop (port 3389)

🧠 **Important**:

- RDP is sensitive → use **low threads**
- Can trigger account lockouts

---

## SNMP community string guessing

### Clean syntax

```bash
hydra -P common-snmp-community-strings.txt <IP> snmp
```

### Explanation

- SNMP uses **community strings**, not usernames
- Password list = possible community strings
- Default strings: `public`, `private`

🧠 **Used when**: SNMP is open (UDP 161).

---

## SMB brute force (very important)

### Clean syntax

```bash
hydra -l Administrator -P words.txt 192.168.1.12 smb -t 1
```

### Explanation

- `-t 1` → **VERY important**
- SMB often locks accounts if too many attempts

🧠 **Used when**: SMB login is allowed (ports 139/445).

---

## SSH brute force

### Clean syntax

```bash
hydra -l root -P passwords.txt <IP> ssh
```

### Explanation

- Tests SSH authentication (port 22)
- Often rate-limited

🧠 **Tip**:

```bash
-t 4
```

is safer for SSH.

---
## Telnet Brute Force

### Clean Telnet syntax

```bash
hydra -l root -P passwords.txt <IP> telnet
```

### With multiple users

```bash
hydra -L users.txt -P pass.txt <IP> telnet
```
# Thread count guidance (exam gold)

| Service | Recommended `-t` |
| ------- | ---------------- |
| FTP     | 16–32            |
| SSH     | 4–8              |
| SMB     | **1**            |
| RDP     | 1–4              |
| MySQL   | 4–8              |
| POP3    | 8–16             |
| telnet  | 4-8              |

---

# Example full workflow (SMB lab)

```bash
nmap -p 445 <IP>
hydra -l Administrator -P pass.txt <IP> smb -t 1
smbclient -L //<IP> -U Administrator
```

---

# ❗ Common mistakes (exam traps)

❌ Forgetting service name  
❌ Using high threads on SMB  
❌ Using `-l` with multiple usernames  
❌ Wrong protocol syntax (`rdp://` required)