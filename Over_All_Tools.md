# CEH Practical Tools Reference

## Network and Information-Gathering Tools

### Ping

```bash
ping
ping 12.32.23.23
```

### Hping3

ICMP ping scan
```bash
sudo hping3 -1 <target_ip>
```

TCP Syn scan

```bash
sudo hping3 -S -p <port> <target_ip>
```

TCP Ack Scan

```bash
sudo hping3 -A -p <port> <target_ip>
```

### WHOIS

```bash
whois
whois 12.32.23.23
```

### DIG

```bash
dig 12.32.23.23
dig 10.10.1.22 ns
dig @nameserver 10.10.1.22 axfr
```

### SSH

```bash
ssh user@ip -i id_rsa
```

- `-i`: Optional flag used when logging in with a private key.

### SCP

```bash
scp user@ip:file_with_path destination_dir
scp user@10.12.23.23:/home/user/id_rsa .
```

### Harvester

```bash
    theHarvester -d microsoft.com -l 50 -b bing
```

## SMB Enumeration

### enum4linux

A tool used to enumerate SMB services.

```bash
enum4linux -a ip
enum4linux -a 12.23.42.43
```

- `-a`: Enumerates all available information, including users and shares.

### smbclient

```bash
smbclient -L -N  10.10.128.80
smbclient -L -N -I 10.10.128.80
smbclient -I 10.10.128.80 -L -N
smbclient //10.10.128.80/Anonymous

smbclient -L 10.10.2.14 -U "Admin"

smbclient //10.10.2.14/share -U "Admin"

```

- `-I`: Specifies the IP address.
- `-L`: Lists available shares.
- `-N`: Does not prompt for a password.


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

Enumerate with smbmap
```bash
smbmap -H <IP>
```

Possible outputs:
- Share names
- Read/Write permissions
- File listings (sometimes!)

If access denied → try guest
```bash
smbmap -H <IP> -u guest -p ""
```

If you have creds
```bash
smbmap -H <IP> -u user -p pass
```

Download files
```bash
smbmap -H <IP> -u user -p pass -r SHARE
```

Or directly:

```bash
smbmap -H <IP> -u user -p pass --download "SHARE/path/file.txt"
```

## SQL Injection

```bash
sqlmap -u <target url with paramter> --cookie <cookie> --dbs
```

> --dbs = is to enumerate dbms databases

once databases are enumerated we need to enumerate tables

```bash
sqlmap -u <target url with paramter> --cookie <cookie> -D <database name> --tables
```

> --tables = enumerate the tables in the database provided

once we have the tables too, we can simply data dump the whole table

```bash
sqlmap -u <target url with paramter> --cookie <cookie> -D <database name> -T <table name> --dump
```

no sql injection attacks can also provide one with an interactive shell too
if thats the case 
we can try

```bash
sqlmap -u <target url with paramter> --cookie <cookie> --os-shell
```

## NFS Enumeration

### Show NFS Exports

```bash
showmount -e ip
```

### Mount an NFS Directory

```bash
mkdir /tmp/newDir
mount 10.2.32.23:/shareDir /tmp/newDir
mount -o rw,ver=2 10.2.32.23:/shareDir /tmp/newDir
```

## Exploit and Vulnerability Research

### SearchSploit

```bash
searchsploit serviceName
searchsploit serviceversion
searchsploit -m exploitNo
searchsploit -e exploitNo
searchsploit --nmap nmapoutput.xml
```

- `-m`: Downloads an exploit to the local machine.
- `-e` or `--examine`: Opens the exploit code on the local machine.
- `--nmap`: Uses an Nmap output file to search for relevant exploits.

## Wireless Security Tools

### Wifite

Wifite is based on the Aircrack-ng tool suite.

```bash
wifite --wpa --kill --dict rockyou.txt
wifite --wpa --dict rockyou.txt --kill
wifite --wpa --dict wordfile --kill
```

### Aircrack-ng

```bash
airmon-ng check
airmon-ng check kill
airmon-ng wlan0 start
airmon-ng wlan0 stop
airodump-ng wlan0mon
airodump-ng wlan0mon --bssid bssid
airodump-ng wlan0mon --bssid bssid -c chNo -w airCat
aireplay-ng wlan0mon -a ap_bssid --deauth deauthCount -c client_bssid
aircrack-ng captureFile -w Passwordfile -b bssid
```

- `airodump-ng wlan0mon`: Lists available access points.
- `--bssid`: Filters the scan to a specific access point.
- `-c`: Specifies the channel.
- `-w`: Specifies the capture-file prefix.
- `aireplay-ng`: Can be used to send deauthentication packets during authorized testing.

### HCX Tools and WPA Hashcat Conversion

```bash
systemctl stop NetworkManager.service
systemctl stop wpa_supplicant.service
hcxdumptool -i wlan0 -o wapcap.pcapng --active_beacon --enable_status=1
hcxdumptool -i wlan0 --do_rcascan
systemctl start NetworkManager.service
systemctl start wpa_supplicant.service
hcxpcapngtool wapcap.pcapng -o wapcap.hc22000 -E essidList
```

The `--do_rcascan` option displays access points with their BSSID or MAC address. After conversion, retain only the required BSSID entries in the `.hc22000` file before using Hashcat.

### Hashcat

If a hash includes a username, use the `--username` flag.

```bash
hashcat -m hashtype -a attacktype hashFile wordfile_brute --username
hashcat -m hashtype -a attacktype hashFile wordfile_bruteforceFormat
hashcat -m 22000 wpa.hc22000 /usr/share/wordlist/rockyou.txt
hashcat -m 22000 wpa.hc22000 -a 3 '?d?d?d?d?d?d?d?d'
hashcat -m 22000 wpa.hc22000 -a 3 'Password@?d?d?d'
hashcat -m 22000 wpa.hc22000 -a 3 -1 '?d?l?u' '?1?1?1?1?1?1?1?1'
hashcat -m 22000 wpa.hc22000 -a 3 --increment --increment-min 8 --increment-max 10 '?d?d?d?d?d?d?d?d?d?d'
```

> -a = attack mode
> -m = hashtype
  > 900 md4
  > 1000 NTLM
  > 1800 SHA512CRYPT
  > 110 SHA1 with SALT HASH
  > 0  MD5
  > 100 SHA1
  > 1400 SHA256
  > 3200 BCRYPT
  > 160 HMAC-SHA1


### John the Ripper

Hashes may be provided with the username separated by a colon, for example: `root:$6$laslxsaa24923`.

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash --format=sha512crypt
john -w=/usr/share/wordlists/rockyou.txt hash
john --list=formats
john hashFileName --show
```

- `--show`: Displays cracked passwords.

## Login and Brute-Force Testing

### Hydra

```bash
hydra -l username -p password ip service_name -s 23
hydra -L usernameList -P passwordList ip service_name -t 64

hydra -L usernameList -P common-snmp-community-strings.txt target.com snmp

hydra -L users.txt -P passwords.txt -s 2222 ssh://<TIP> -f


```

- `-l`: Specifies a single username.
- `-L`: Specifies a username list.
- `-p`: Specifies a single password.
- `-P`: Specifies a password list.
- `-s`: Specifies a non-default service port.
- `-t`: Specifies the number of threads per target. Maximum: `64`.

## Web Enumeration

### Gobuster

```bash
gobuster dir -u url -w wordfile -t 1000 -o outputfile -k -x html,php,js,config
gobuster vhost -u url -w wordfile -t 1000 -o outputfile -k --append-domain
gobuster dns -d google.com -w wordfile -i -t 1000 -o output
```

- `-k`: Skips SSL certificate verification.
- `-x`: Specifies file extensions.

### Ffuf

```bash
ffuf -u http://example.com/FUZZ -w wordfile -t 1000
ffuf -u http://example.com/FUZZ -w wordfile -t 1000 -e html,php
ffuf -u http://FUZZ.example.com/ -w wordfile -t 1000
```

### WPScan

```bash
wpscan --update
wpscan --url http://example.com --enumerate
wpscan --url http://example.com -e t,p,u,at,pt,vt,vp
wpscan --url http://example.com --usernames gana --passwords wordfile
```
Bruteforce:

```bash
wpscan -url <domain_or_ip> -U user -P pass.txt
```

Enumerate users:

```bash
wpscan -url <domain_or_ip> -e u

Useful WordPress paths:

```text
/wp-content/themes/themename
/wp-content/plugins/pluginname
```

### Nikto

```bash
nikto --list-plugins
nikto -h url_ip_domainName -Plugin pluginName -Display 1_2_E -Tuning 1_2
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

## Steganography Tools and Tips

### File Inspection Tools

Check file metadata
```
exiftool image.jpg
```
```bash
file filename
strings filename
xxd filename
hexdump filename
```

- `file`: Identifies the file type.
- `strings`: Extracts readable strings from a file.
- `xxd`: Creates a hexadecimal representation of a file.
- `hexdump`: Displays file contents in hexadecimal format.
- `hexeditor`: Edits file data at the byte level.
- `binwalk`: Checks whether a binary file contains embedded files.

### Steghide

A tool used to hide and extract data in files.

```bash
steghide --embed -cf coverFile -ef hiddenfile
steghide --extract -sf coverFile
steghide --info coverFile
```

### Stegseek and Stegbrute

- **Stegseek:** Brute-forces passphrase-protected steganography JPG files.Fast tool for cracking passwords hidden in JPG images created with steghide.

```
stegseek image.jpg wordlist.txt
```
Quiet mode
```
stegseek secret.jpg rockyou.txt -q
```
Brute force and store the result in text file
```
stegseek secret.jpg rockyou.txt -xf output.txt
```

- **Stegbrute:** Brute-forces passphrase-protected steganography files. Password brute-force tool for steghide files.
```
stegbrute image.jpg wordlist.txt
```
Character bruteforce:
```
stegbrute secret.jpg -b
```
Specifying password length
```
stegbrute secret.jpg -b -l 4
```
Verbose output
```
stegbrute secret.jpg rockyou.txt -v
```

### 7-Zip

A tool used to extract ZIP archives and inspect their contents.

```bash
7z l archive.zip
7z x archive.zip
```

## Hashing Calculations:
```
    sha256sum file.txt

    md5sum file.txt
```
Windows:

```
HashMyFiles.exe
```

## CRC32 calculations for APK

```
    crc32 malware.apk
```
## Windows cmds:
To view text file
```

    type note.txt
```

## File retrieval

FTP
```bash
get <filename>
```

RDP

create a shareable folder
```bash
mkdir -p /home/attacker/rdp_share
```
give permissions
```bash
chmod 755 /home/attacker/rdp_share
```

drive redirect
```bash
xfreerdp /v:<TIP> /u:username /p:password /drive:share,/home/attacker/rdp_share
```
copy the file needed in the folder on the remote machine
it'll be under `Network Locations / Network Drives` 
under a location called share on <clientname>

SSH

file
```bash
scp user@<TIP>:/path/file.txt newname.txt
```

folder

```bash
scp -r username@<TIP>:/path/to/folder .
```

If SSH access exists but password auth is blocked. Use key:
```bash
scp -i key.pem user@10.10.10.10:/file .
```

### Hash-Identifier
```bash
hash-identifier
```



## RDP

CLI:

```bash
xfreerdp /v:<TIP> /u:username
```

GUI:

```bash
remmina
```

### Telnet

```bash
telnet <IP>
```

### Mysql
```bash
mysql -u <username> -h <TIP> -p <password>
```
> if its running on a custom port use `-P` to specify the port


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

```bash
find / -name <filename> -type f 2>/dev/null
```

Windows
```powershell
dir /b/s <filename*>
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


### Vulnerability Scanning

Nmap vulnerability scan:

```bash
nmap -Pn --script vuln <TIP> -T5
```

Alternative if nmap fails:

- `openVAS`


### DVWA Command Execution

Windows:

```cmd
| type "path"
```

Linux:

```bash
| cat "path"
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
9871, 6703 → RAT
```


## Nmap Scanning Types

### TCP Connect Scan (`-sT`)

A TCP connect scan completes the three-way handshake.

- **Open:** The three-way handshake is completed.
- **Closed:** An RST flag is received in response to the SYN flag.
- **Filtered:** No response is received.

This scan is slower, and a firewall may be configured to send RST flags for open ports as well.

### TCP SYN Scan (`-sS`)

Also called a half-open or SYN scan, this method is generally faster and less intrusive than a TCP connect scan.

- **Open:** An ACK is received in response to the SYN, after which an RST is sent.
- **Closed:** An RST is received in response to the SYN.
- **Filtered:** No response is received.

### UDP Scan (`-sU`)

UDP scanning sends packets without establishing a connection.

- **Open or filtered:** No response is received.
- **Open:** A UDP response is received.
- **Closed:** An ICMP port-unreachable response is received.

### Other Scanning Techniques

These scans use less common TCP flag combinations and may be more stealthy than a SYN scan.

#### Null Scan (`-sN`)

Sends a TCP packet with no flags set. An RST response indicates that the port is closed.

#### FIN Scan (`-sF`)

Sends a TCP packet with the FIN flag set. An RST response indicates that the port is closed.

#### Xmas Scan (`-sX`)

Sends a TCP packet with the FIN, PSH, and URG flags set. An RST response indicates that the port is closed.

For Null, FIN, and Xmas scans, no response generally indicates that the port is open or filtered. An ICMP error response may indicate that the port is filtered.



## Metasploit Framework

### Important Locations

- Installation directory: `/usr/share/metasploit-framework`
- Custom modules directory: `~/.msf5/modules`

The `modules` directory contains modules used by Metasploit, including:

- `auxiliary`: Port scanning, fuzzing, and sniffing modules.
- `encoders`: Payload encoders.
- `exploits`: Modules that take advantage of vulnerabilities.

### Loading Custom Modules

```bash
msfconsole -m ~/secret-module/
```

Inside the Metasploit console:

```text
msf> loadpath ~/secret-module/
```

### Initializing the Metasploit Database

The database stores the results of scans and other activities.

```bash
service postgresql start
msfdb init
```

### wireshark

Search by request method
```
    http.request.method == POST

    http.request.method == "POST"

```
Search by host name
```
    http.host == "example.com"

    http.host contains "www.moviescope.com"
```
Search by src and dst ip
```
    ip.src == 1.2.3.4

    ip.dst == 1.2.3.4
```
Search by request method & hostname or ip
```
    http.request.method == "POST" && http.host == "example.com"

    http.request.method == "POST" && ip.dst == 1.2.3.4
```
Search by protocol 
```
udp
```


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



#### Characteristics to find flooding or DOS:
```
Very high packet rate
Large number of packets from one or many source IPs
Same destination IP and port repeatedly
Few or no responses from the destination
```

search by protocol combined with ip
```
udp && ip.dst == 192.168.1.10
```
Then:

Statistics → Conversations → UDP

Look for:
```
Top talkers
Packet counts
Bytes transferred
```
A UDP flood usually has one conversation with an abnormally large packet count.

#### Use I/O Graphs to Confirm Flooding

Open:

Statistics → I/O Graphs

```
udp
```
A UDP flood appears as a sharp spike or sustained high packet-per-second rate.

Filter by port
``` 
udp.port == 53
```

#### Fastest Method to find dos or flooding attack
```
udp
```
Open Statistics → Conversations → UDP.

Sort by Packets.

```
Note:

Source IP(s)
Destination IP
Destination Port
Check the Protocol column or well-known port mapping to identify the service.
```

#### find RDP (Remote Desktop Protocol) traffic in Wireshark, use these filters:

##### Filter by Standard RDP Port (3389)

```
tcp.port == 3389
```

Or specifically for destination port:

```
tcp.dstport == 3389
```

Source port:
```
tcp.srcport == 3389
```
```
rdp
```
#####  Identify New RDP Sessions
```
tcp.port == 3389 && tcp.flags.syn == 1
```

##### Detect Possible RDP Brute Force Activity

```
tcp.port == 3389
```
Then:

Statistics → Conversations → TCP

Look for:

One source IP
Hundreds/thousands of connections
Repeated SYN packets

##### Find All Hosts Offering RDP

Open:

Statistics → Endpoints → TCP

Or filter:

```
tcp.srcport == 3389
```

Systems responding from source port 3389 are usually RDP servers.

#### Detect ARP Spoofing (Common Hijacking Technique)

Many session hijacks begin with ARP poisoning.

Filter ARP traffic:

```
arp
```

Duplicate IP-MAC mappings:

```
arp.duplicate-address-detected
```

Gratuitous ARP:

```
arp.src.proto_ipv4 == arp.dst.proto_ipv4
```

Signs:

One IP associated with multiple MAC addresses
Frequent unsolicited ARP replies

#### Detect Duplicate TCP Sessions (Possible TCP Hijacking)

Look for retransmissions, resets, and out-of-order packets:

```
tcp.analysis.retransmission
```
```
tcp.analysis.out_of_order
```
```
tcp.flags.reset == 1
```
```
tcp.analysis.duplicate_ack
```

Signs:

    1. Sudden TCP RST packets
    2. Duplicate ACK storms
    3. Sequence number anomalies
    4. Unexpected source IP sending packets in an existing session

#### Show All RPC/DCERPC Traffic
```
dcerpc

rpc
```
#### RPC over SMB (Common on Windows)

RPC often runs over SMB named pipes:

```
smb2 && dcerpc

smb && dcerpc
```

#### Detect RPC Endpoint Mapper Activity
```
tcp.port == 135

dcerpc || tcp.port == 135

```

#### Detect Potential RPC Scanning

Many bind attempts:

```
dcerpc.cn_bind
```

Statistics → Conversations → TCP

Look for:

1. One source contacting many hosts
2. Many bind requests in a short period
3. Numerous faults

#### MAC flooding detection:

```
    eth 

    eth.add == ip
```

## Privilege-Escalation Tools

- **LinEnum**
- **LinPEAS**

### web applicaiton pentesting tools
1. owaspzap
2. smartscanner - UI - windows
3. wapiti - cli
4. snipper - cli
5. skipfish - cli


## Common TCP ports

| protocol name                                    | TCP port   | UDP port |
| ------------------------------------------------ | ---------- | -------- |
|                                                  | 2002       |          |
| ADB (android debug bridge)                       | 5555       |          |
| Default port of portable python server           | 8000       |          |
| DNS                                              | 53         | 53       |
| FTP                                              | 21         |          |
| HTTP                                             | 80         |          |
| HTTPS                                            | 443        |          |
| Kerberos-sec                                     | 88         |          |
| LDAP                                             | 389        |          |
| LDAPS (LDAP over SSL/TLS)                        | 636        |          |
| Microsoft ADGC (active directory global catalog) | 3268, 3269 |          |
| Modbus (industrial communications)               | 502        |          |
| MQTT (IOT and OT)                                | 1833       |          |
| MSSQL                                            | 1433       |          |
| MySQl                                            | 3306       |          |
| NFS                                              | 2049       |          |
| njrat default port                               | 5553       |          |
| Proxy / WAMP server                              | 8080       |          |
| RAT Ports                                        | 9871, 6703 |          |
| RDP                                              | 3389       |          |
| RPC                                              | 135        |          |
| SMB                                              | 445        |          |
| SMB (NetBIOS)                                    | 139        |          |
| SNMP                                             |            | 161, 162 |
| SSH                                              | 22         |          |
| Telnet                                           | 23         |          |
| SMTP                                             |25,2525,587 |          |
| POP3                                             | 110        |          |
| POP3 (encrypted)                                 | 995        |          |
some common remote login protocols

- **FTP** → `ftp <ip>`
- **SSH** → `ssh user@<ip>`
- **Telnet** → `telnet <ip> <port>`
- **SMB (smbclient)** → `smbclient //<ip>/<share>`
- **RDP** → `xfreerdp /v:<ip>`
- **MySQL** → `mysql -h <ip> -u user -p`
- **ADB** → `adb connect <ip>:5555`


## Common UDP ports:

| Port  |	Service                 |
| ------|---------------------------|
| 53    |	    DNS                 |
| 67/68 |	    DHCP                |
| 69    |	    TFTP                |
| 123   |	    NTP                 |
| 137   |	    NetBIOS Name Service| 
| 161   |	    SNMP                |
| 500   |	    IKE/IPsec           |
| 514   |	    Syslog              |
| 1900  |	    SSDP                |
| 4500  |	    IPsec NAT-T         |


## important links:
1. GTFObins

2. https://ceh-practical.cavementech.com/module-2.-footprinting-and-reconnaissance/3.-footprinting-through-social-networking-sites

3. https://github.com/hari-the-billionaire/CEH-v13-Engage-Skill-Check/blob/main/CEH%20Skill%20Check%20Engage%20Part%201.md