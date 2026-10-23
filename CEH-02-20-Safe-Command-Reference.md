# CEH Practical Command Handbook
## Modules 02–20

This handbook reorganizes the command-line material in the numbered CEH notes by module. Each command has a title and a short description. Replace placeholders in angle brackets with values from a system you own or are explicitly authorized to assess.

> **Scope and safety:** Some source sections contain commands for credential theft, password cracking, malware and remote shells, denial-of-service, stealth or firewall evasion, session interception, wireless attacks, SQL data extraction, and unauthorized cloud changes. Those commands are not reproduced here. The relevant module headings are retained, with a safe study or defensive alternative where useful.

## Source comparison: attack topics and command coverage

The table below records the attack topics found during comparison with the original modules. “Not reproduced” means the original procedure or command is intentionally absent; the safe commands elsewhere in this handbook are alternatives for administration, monitoring, or authorized lab work, not substitutes for executing the attack.

| Module | Attack topics in the source notes | Handbook coverage |
|---|---|---|
| 02 — Footprinting | Search-operator reconnaissance, social-profile and email discovery, domain/DNS/network footprinting, and Recon-ng modules | WHOIS, DNS lookups, route tracing, and bounded Recon-ng workspace/report commands are included. Automated harvesting and broader target-enumeration procedures are not reproduced. |
| 03 — Scanning | Host discovery, port/service/OS scanning, Metasploit scanner workflows, and firewall/IDS evasion | Basic authorized host discovery and service/version checks are included. Evasion options and automated scanner workflows are not reproduced. |
| 04 — Enumeration | NetBIOS, SNMP, LDAP, NFS/RPC, DNS zone transfer, SMTP user/relay, and SMB share enumeration | Basic local/authorized NetBIOS, DNS, NFS-port, and authenticated SMB checks are included. Credential/community-string enumeration and user/share harvesting procedures are not reproduced. |
| 05 — Vulnerability analysis | OpenVAS, Nikto, Nmap vulnerability scripts, and Skipfish web scans | Trivy image assessment is included. Active web scanner procedures are described but not supplied as commands. |
| 06 — System hacking | LLMNR/NBT-NS credential capture; reverse shells; buffer-overflow fuzzing and control-flow steps; UAC/sticky-keys bypass; persistence; log clearing; AS-REP roasting; password spraying; MSSQL attacks; privilege escalation; and Kerberoasting | Defensive local inventory commands are included. Credential capture/cracking, exploit construction, privilege escalation, persistence, log tampering, and AD attack commands are not reproduced. |
| 07 — Malware threats | Trojan/RAT operation, virus/worm creation, and malware analysis | Non-executing file identification and hashing are included. Malware creation, deployment, persistence, and remote-control commands are not reproduced. |
| 08 — Sniffing | MAC flooding, DHCP starvation, packet/remote capture, and ARP poisoning | Safe display filters and a promiscuous-mode detection check are included. Traffic disruption, poisoning, and interception procedures are not reproduced. |
| 09 — Social engineering | Credential-harvesting pages, phishing, and impersonation | No phishing or credential collection commands/prompts are included. Awareness-training guidance is provided. |
| 10 — Denial of Service | TCP/UDP/ICMP/HTTP floods, botnet-driven traffic, and stress-test tools | Local connection diagnostics are included. Flooding, botnet, and attack-tool commands are not reproduced. |
| 11 — Session hijacking | Proxy interception, session redirection, and capture of HTTP credentials | Local DNS-cache clearing and response-header inspection are included. Session interception and hijacking procedures are not reproduced. |
| 12 — IDS, firewalls, and honeypots | IDS/firewall evasion, BITSAdmin-based activity, and honeypot setup | Defensive Snort configuration, validation, and monitoring commands are included. Evasion and covert execution procedures are not reproduced. |
| 13 — Web servers | FTP password brute force and Log4Shell exploitation/reverse-shell workflow | Authorized service/banner and TLS inspection are included. Brute-force and remote-code-execution procedures are not reproduced. |
| 14 — Web applications | Login brute force and exploitation of a vulnerable WordPress plugin | ZAP launch and safe-scope guidance are included. Brute-force and plugin-exploitation commands are not reproduced. |
| 15 — SQL injection | SQLi discovery, database/table enumeration, data extraction, and OS-shell access | Defensive guidance on parameterized queries and least privilege is included. Exploitation and data-extraction commands are not reproduced. |
| 16 — Wireless | Monitor-mode capture, deauthentication, WPA handshake capture, and password cracking | Interface inventory is included. Deauthentication, handshake capture, and cracking commands are not reproduced. |
| 17 — Mobile | Remote Android control and malicious APK/RAT creation | ADB commands for explicitly authorized devices are included. Covert control and malicious APK commands are not reproduced. |
| 18 — IoT and OT | Public device discovery and CAN traffic capture/replay | A virtual CAN simulator and passive observation of its local test interface are included. Real-device targeting and replay against operational systems are not reproduced. |
| 19 — Cloud | Azure tenant/user enumeration, S3 object discovery/modification, and IAM privilege escalation | Read-only AWS identity/security checks and image assessment are included. Tenant probing, bucket modification, and privilege-changing commands are not reproduced. |
| 20 — Cryptography | Encryption, self-signed certificates, disk encryption, and steganography | Certificate/hash inspection guidance is included. GUI-only encryption workflows are summarized; steganographic concealment commands are not reproduced. |

## Contents

- [02 — Footprinting and Reconnaissance](#02--footprinting-and-reconnaissance)
- [03 — Scanning Networks](#03--scanning-networks)
- [04 — Enumeration](#04--enumeration)
- [05 — Vulnerability Analysis](#05--vulnerability-analysis)
- [06 — System Hacking](#06--system-hacking)
- [07 — Malware Threats](#07--malware-threats)
- [08 — Sniffing](#08--sniffing)
- [09 — Social Engineering](#09--social-engineering)
- [10 — Denial of Service](#10--denial-of-service)
- [11 — Session Hijacking](#11--session-hijacking)
- [12 — IDS, Firewalls, and Honeypots](#12--ids-firewalls-and-honeypots)
- [13 — Hacking Web Servers](#13--hacking-web-servers)
- [14 — Hacking Web Applications](#14--hacking-web-applications)
- [15 — SQL Injection](#15--sql-injection)
- [16 — Hacking Wireless Networks](#16--hacking-wireless-networks)
- [17 — Hacking Mobile Platforms](#17--hacking-mobile-platforms)
- [18 — IoT and OT Hacking](#18--iot-and-ot-hacking)
- [19 — Cloud Computing](#19--cloud-computing)
- [20 — Cryptography](#20--cryptography)

---

## 02 — Footprinting and Reconnaissance

### WHOIS domain lookup

Displays public registration information for a domain.

```bash
whois <authorized-domain>
```

### DNS record lookups with `dig`

Query a domain's address and common DNS records.

```bash
dig <authorized-domain>
dig <authorized-domain> A
dig <authorized-domain> NS
dig <authorized-domain> MX
```

### DNS lookups with `nslookup`

Start the interactive DNS client, choose a query type, and enter the domain to look up.

```cmd
nslookup
```

At the `nslookup` prompt:

```text
set type=a
<authorized-domain>
set type=cname
<authorized-domain>
```

### Trace the network path

Show the routers traversed while reaching an authorized destination. The Windows command limits the trace to five hops.

```cmd
tracert -h 5 <authorized-domain>
```

```bash
traceroute <authorized-domain>
```

### Recon-ng workspace and module discovery

Start Recon-ng, view help and available modules, and create or list workspaces. Module use can make external requests; keep activity within the approved scope.

```text
recon-ng
help
modules search
workspaces
workspaces create <workspace-name>
workspaces list
```

### Recon-ng report generation

Load the HTML reporting module, set report metadata, and run it.

```text
modules load reporting/html
options set FILENAME <path-to-report.html>
options set CREATOR <your-name>
options set CUSTOMER <authorized-customer>
run
```

---

## 03 — Scanning Networks

Use host and service discovery only on an authorized lab or network.

### Find responding hosts with `fping`

Probe the lab subnet and print responding addresses.

```bash
fping -a -g <authorized-lab-cidr> 2>/dev/null
```

### Nmap host discovery

The `-sn` option performs host discovery without a port scan. Select the probe type that is appropriate for your lab.

```bash
nmap -sn -PR <authorized-host>
nmap -sn -PU <authorized-host>
nmap -sn -PE <authorized-host>
nmap -sn -PE <authorized-lab-range>
nmap -sn -PP <authorized-host>
nmap -sn -PS <authorized-host>
nmap -sn -PA <authorized-host>
nmap -sn -PM <authorized-host>
nmap -sn -PO <authorized-host>
```

`-PR`, `-PU`, `-PE`, `-PP`, `-PS`, `-PA`, `-PM`, and `-PO` select ARP, UDP, ICMP echo, ICMP timestamp, TCP SYN, TCP ACK, ICMP address-mask, and IP-protocol discovery respectively.

### TCP connect scan

Check TCP ports on an authorized host using a full TCP connection. `-Pn` skips the host-discovery stage.

```bash
nmap -sT -v -Pn <authorized-host>
```

### Service-version and operating-system checks

Identify service versions or attempt OS identification on an authorized host.

```bash
nmap -sV -p <authorized-ports> <authorized-host>
nmap -O <authorized-host>
```

### SMB host information check

Run the Nmap SMB OS discovery script only against an authorized lab host.

```bash
nmap --script smb-os-discovery.nse <authorized-host>
```

### Nmap help

Display command-line usage information.

```bash
nmap --help
```

Stealth scans, packet fragmentation, source-port manipulation, decoys, spoofing, and other evasion procedures from the source notes are intentionally omitted.

---

## 04 — Enumeration

### View NetBIOS information

On Windows, display the NetBIOS name table for an authorized remote host or the local name cache.

```cmd
nbtstat -a <authorized-host>
nbtstat -c
```

### Review current Windows network connections

Display configured network connections and shares available to the current user.

```cmd
net use
```

### Check for NFS service availability

Check the standard NFS port on an authorized host.

```bash
nmap -p 2049 <authorized-host>
```

### DNS authority records

Look up a domain's name servers and Start of Authority record.

```bash
dig NS <authorized-domain>
dig SOA <authorized-domain>
```

### List SMB shares with an authorized account

List shares on a system you administer. Do not attempt anonymous access or access data without permission.

```bash
smbclient -L //<authorized-host> -U <authorized-user>
```

---

## 05 — Vulnerability Analysis

### Scan a container image with Trivy

Check an image for known vulnerabilities before using it. Use the image and tag approved for your project.

```bash
trivy image <image-name>:<tag>
```

### Web vulnerability scanners

The source notes include Nikto, Skipfish, Nmap vulnerability scripts, and OpenVAS. Active web scans can affect availability or expose data; run them only in an approved test environment with explicit scope. Exploit-oriented scan commands are not reproduced.

---

## 06 — System Hacking

The source chapter includes LLMNR/NBT-NS credential capture, password cracking, reverse-shell generation, buffer-overflow testing, privilege escalation, persistence, event-log clearing, and Active Directory attacks such as password spraying and Kerberoasting. Attack commands are omitted.

### Review local accounts and processes

Use these defensive inventory commands on a system you administer.

```bash
getent passwd
ps aux
```

```powershell
Get-LocalUser
Get-Process
```

---

## 07 — Malware Threats

The source chapter describes creating and deploying trojans, worms, and other malware, as well as static and dynamic analysis. Payload-generation, persistence, and remote-control commands are omitted. Analyze unknown files only in an isolated environment; do not execute them on a production host.

### Safely identify a suspicious file

Inspect file metadata and calculate a hash without executing the file.

```bash
file <suspicious-file>
sha256sum <suspicious-file>
```

Use an isolated malware-analysis environment and follow your organization's handling procedures.

---

## 08 — Sniffing

The source chapter includes MAC flooding, DHCP starvation, remote packet capture, and ARP poisoning. Traffic disruption, poisoning, and credential-interception procedures are omitted.

### Wireshark display filters

Use these filters to narrow an authorized packet capture to common protocol traffic.

```text
dns
tls
icmp
```

### Detect a promiscuous-mode interface

Run the Nmap detection script only on an authorized lab host or range.

```bash
nmap --script=sniffer-detect <authorized-host-or-range>
```

---

## 09 — Social Engineering

The source chapter discusses credential-harvesting pages and phishing lures. Commands and prompts that facilitate phishing or credential collection are omitted. For awareness training, use an approved simulation platform and measure aggregate, non-sensitive outcomes.

---

## 10 — Denial of Service

Traffic-flooding and botnet commands in the source chapter are omitted.

### Review local connection statistics

Use these commands to help diagnose unusual connection volume on a Linux system you administer.

```bash
ss -s
ss -ant state syn-recv
```

For load testing, use an approved service and a written test plan with agreed limits and a stop condition.

---

## 11 — Session Hijacking

Proxy-based interception and session hijacking procedures are omitted.

### Clear the local Windows DNS cache

Clear cached DNS entries while troubleshooting name resolution on your own Windows machine.

```cmd
ipconfig /flushdns
```

### Inspect web security headers

Review the response headers from an authorized web service.

```bash
curl -sS -D - -o /dev/null https://<authorized-web-host>
```

Check that session cookies use appropriate `Secure`, `HttpOnly`, and `SameSite` attributes.

---

## 12 — IDS, Firewalls, and Honeypots

Firewall and IDS evasion and covert execution procedures are omitted. The source also covers honeypots; the following Snort commands are for configuring and verifying defensive monitoring on a machine you administer.

### List Snort network interfaces

Run from an elevated Windows command prompt to display available interfaces and their indices.

```cmd
snort -W
```

### Check Snort configuration

Validate the Snort 2 configuration before starting a capture.

```cmd
snort -T -c C:\Snort\etc\snort.conf
```

### Run Snort in verbose packet-display mode

Replace `<interface-index>` with the interface index displayed by `snort -W`.

```cmd
snort -dev -i <interface-index>
```

### Run Snort with IDS rules and console alerts

Start Snort with the selected configuration and log directory.

```cmd
snort -i <interface-index> -A console -c C:\Snort\etc\snort.conf -l C:\Snort\log -K ascii
```

### Generate a benign test event

With Snort monitoring your lab, generate a single connectivity check to a destination you are authorized to contact.

```cmd
ping <authorized-host>
```

---

## 13 — Hacking Web Servers

Exploit, brute-force, and reverse-shell commands from the source chapter are omitted.

### Inspect an HTTP response

Retrieve the root page headers from an authorized HTTP server.

```bash
curl -sS -I http://<authorized-web-host>/
```

### Check service versions

Check the HTTP service version on an authorized server.

```bash
nmap -sV -p 80,443 <authorized-web-host>
```

### Read a server banner with Netcat

Connect to an authorized HTTP service, then enter the request shown. Press Enter twice after the request line to end the headers.

```bash
nc -v <authorized-web-host> 80
```

```http
GET / HTTP/1.0
Host: <authorized-web-host>
```

### Inspect TLS certificate details

Display the certificate information presented by an authorized HTTPS host.

```bash
openssl s_client -connect <authorized-web-host>:443 -servername <authorized-web-host> </dev/null
```

---

## 14 — Hacking Web Applications

Brute-force and vulnerable-plugin exploitation procedures from the source chapter are omitted.

### Launch OWASP ZAP

Start the ZAP interface. Configure the target scope to a local or explicitly authorized test application before scanning.

```bash
zaproxy
```

The source also lists Skipfish and Sniper. Their active scan commands are omitted here because they can send substantial traffic and require careful target authorization and scope.

---

## 15 — SQL Injection

SQL injection exploitation, database enumeration, data dumping, and OS-shell commands from the source chapter are omitted.

For defense, use parameterized queries, least-privilege database accounts, and a controlled test environment. OWASP ZAP is listed in the source notes for reviewing an authorized test application.

---

## 16 — Hacking Wireless Networks

Wireless monitor-mode capture, deauthentication, handshake capture, and password-cracking commands are omitted.

### Review local network interfaces

Display network interface configuration for troubleshooting on your own machine.

```bash
ifconfig
```

Use your operating system's approved wireless-management interface to inventory networks you are authorized to administer.

---

## 17 — Hacking Mobile Platforms

RAT construction, covert access, and unauthorized mobile-device collection commands are omitted.

### List authorized ADB-connected devices

Display Android devices that have explicitly authorized this computer for debugging.

```bash
adb devices
```

### Open a shell on an authorized device

Use only with a device you own or administer and have explicitly authorized for ADB.

```bash
adb shell
```

### Transfer files with ADB

Copy a specific file from an authorized device to the computer, or copy a file to a chosen location on the device.

```bash
adb pull <device-file-path> <local-destination>
adb push <local-file-path> <device-destination>
```

### Reboot an authorized device

```bash
adb reboot
```

---

## 18 — IoT and OT Hacking

The following commands are limited to the source's virtual CAN simulator workflow. Do not run CAN commands against a real vehicle or operational system.

### Install CAN utilities

Install the Linux CAN tools package in a dedicated lab virtual machine.

```bash
sudo apt-get install can-utils
```

### Set up a virtual CAN interface

Load the CAN modules, create the `vcan0` virtual interface, and bring it up.

```bash
sudo modprobe can
sudo modprobe vcan
sudo ip link add dev vcan0 type vcan
sudo ip link set up vcan0
ip link show vcan0
```

### Build the ICSim simulator

From the local ICSim source directory, build the simulator binaries.

```bash
cd ICSim
make
```

### Start the simulator and control panel

Run each program in a separate terminal, connected only to the virtual interface.

```bash
./icsim vcan0
```

```bash
./controls vcan0
```

### Observe virtual CAN traffic

Use a separate terminal to inspect or save traffic generated by the simulator.

```bash
cansniffer -c vcan0
candump -l vcan0
```

Replay commands are intentionally excluded; never replay CAN logs against a real vehicle or OT network.

---

## 19 — Cloud Computing

Azure tenant/user enumeration, S3 object modification, privilege escalation, broad access-policy changes, and bucket write/delete commands from the source chapter are omitted.

### Install and import AADInternals

These setup commands install the PowerShell module. Use organization-approved module sources and a dedicated admin workstation.

```powershell
Install-Module AADInternals
Import-Module AADInternals
```

### Review an AWS identity and account configuration

The first command verifies the identity associated with the configured credentials. The next two are read-only checks for an AWS account and bucket you administer.

```bash
aws sts get-caller-identity
aws iam get-account-summary
aws s3api get-public-access-block --bucket <your-bucket>
```

### Assess a container image

Use Trivy to review an image for known vulnerabilities before deployment.

```bash
docker pull <image-name>:<tag>
trivy image <image-name>:<tag>
```

---

## 20 — Cryptography

The source chapter primarily describes GUI workflows for CyberChef, CryptoForge, IIS certificates, VeraCrypt, Steghide, and Snow. The following command-line examples are limited to file integrity and certificate inspection; file-hiding commands are omitted.

### Calculate a SHA-256 file digest

Use a digest to verify that a file has not changed.

```bash
sha256sum <file>
```

### Inspect a PEM certificate

Display the subject, issuer, and validity dates of a certificate.

```bash
openssl x509 -in <certificate.pem> -noout -subject -issuer -dates
```

For encryption, use a reputable tool such as VeraCrypt, select a strong unique passphrase, and store recovery information securely.
