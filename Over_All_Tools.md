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
smbclient -L -N -I 10.10.128.80
smbclient -I 10.10.128.80 -L -N
smbclient //10.10.128.80/Anonymous
```

- `-I`: Specifies the IP address.
- `-L`: Lists available shares.
- `-N`: Does not prompt for a password.

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

## Privilege-Escalation Tools

- **LinEnum**
- **LinPEAS**

## Steganography Tools and Tips

### File Inspection Tools

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

- **Stegseek:** Brute-forces passphrase-protected steganography files.
- **Stegbrute:** Brute-forces passphrase-protected steganography files.

### 7-Zip

A tool used to extract ZIP archives and inspect their contents.

```bash
7z l archive.zip
7z x archive.zip
```

### web applicaiton pentesting tools
1. owaspzap
2. smartscanner - UI - windows
3. wapiti - cli
4. snipper - cli
5. skipfish - cli

### important links:
1. GTFObins
2. https://ceh-practical.cavementech.com/module-2.-footprinting-and-reconnaissance/3.-footprinting-through-social-networking-sites

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


#### Characteristics to find flooding or DOS:

Very high packet rate
Large number of packets from one or many source IPs
Same destination IP and port repeatedly
Few or no responses from the destination


search by protocol combined with ip
```
udp && ip.dst == 192.168.1.10
```
Then:

Statistics → Conversations → UDP

Look for:

Top talkers
Packet counts
Bytes transferred

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

Note:
Source IP(s)
Destination IP
Destination Port
Check the Protocol column or well-known port mapping to identify the service.


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

Sudden TCP RST packets
Duplicate ACK storms
Sequence number anomalies
Unexpected source IP sending packets in an existing session

## Common UDP ports:

Port	Service
53	    DNS
67/68	DHCP
69	    TFTP
123	    NTP
137	    NetBIOS Name Service
161	    SNMP
500	    IKE/IPsec
514	    Syslog
1900	SSDP
4500	IPsec NAT-T

