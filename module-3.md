# Scanning Networks

> it refers to a set of procedures performed to identify the hosts, ports and services running in a network

### host discovery 
various types of techniques
- ARP ping
- UDP ping
- ICMP ping (icmp echo ping, icmp timestamp, ping icmp, address mask ping)
- TCP ping (tcp syn ping , tcp ack ping)
- IP protocol ping

tool used
#### [[Nmap]] #tool/linux 
```bash
nmap -sn -PR <target-ip>
```
> -sn = disables port scan
> -PR = performs ARP ping

```bash
nmap -sn -PU <target-ip>
```
> -PU = perform UDP scan

```bash
nmap -sn -PE <target-ip>
```
```bash
nmap -sn -PE <target-ip-range>
```

> -PE = performs ICMP ECHO ping scan

```bash
nmap -sn -PP <target-ip>
```
> -PP = ICMP timestamp ping scan

more flags
> -PM = ICMP Address Mask Ping Scan
> -PS = TCP SYN ping scan
> -PA = TCP ACK ping scan
> -PO = IP protocol ping scan - sends different probe packets of different IP protocols to the target host, any response from any probe indicates that a host is active

### Port and Service Discovery

tool used
#### [[Zenmap]] #tool/windows #gui 

> it is the gui version of nmap and specifically made for the windows machine
> hence the commands are the same 

```bash
nmap -sT -v -Pn <target-ip>
```
> -sT = performs TCP connect/full open scan
> -v = enables verbose output
> -Pn = skips host discovery

> after finishing the scan check out various other tabs for more details

```bash
nmap -sS -v <target-ip>
```
> -sS = stealth scan - tcp half open scan

```bash
nmap -sX -v <target-ip>
```
> -sX = performs the Xmas scan

other flags 
> -sM = tcp maimon scan
> -sA = ACK flag probe scan
> -sU = UDP scan
> -sN = NULL scan

> -T(1-5) = timing template (1 is slow , 5 is very fast , 4 is good)
> -A = enables aggressive scan ( chances of getting detected are more)

general interpretation of results from nmap scan
> open = port is open
> filtered = port is behind a firewall
> closed = closed port

> > if the port is closed the scan receives a RST packet in response
> >but if the port is open | filtered various other responses might be visible in the wireshark if the packet capturing is turned on

other flags
> -sI = IDLE/IPIDD header scan
> -sY = SCTP INIT scan
> -sZ = SCTP cookie echo scan

> -sV = version detect (very important flag)

> -p- = to discover all the open ports on the target

### OS discovery

can be done by banner grabbing
two types of banner grabbing
1. active banner grabbing
2. passive banner grabbing

tool used
#### nmap #tool/linux 

```bash
nmap -A <target-ip>
```
> -A = enables aggressive scan which scans for various things at once
>> check under *host script results*

```bash
nmap -O <target-ip>
```
> -O = enables OS discovery

```bash
nmap --script smb-os-discovery.nse <target-ip>
```
> --script = specifies the customized script
>smb-os-discovery.nse - attempts to determine OS, computer name, domain, workgroup, current time over SMB protocol (port 445 or 139)

```bash
nmap -O --script=default --osscan-guess
```

### Scan Beyond Firewall

sometimes the port is opened but it is behind a firewall and hence can not be used or exploited in a way that an open port can be
hence we need some way or tool or techniques to bypass the firewall and exploit what's hidden behind it (exploit IDS/firewall limitations)

Various ways to evade firewall/IDS

1. packet fragmentation 
2. source routing - specifies the routing path for the malformed packet to reach the intended target
3. source port manipulation - manipulate the source port with a common source port to evade IDS/firewall
4. IP Address decoy - decoy IP addresses 
5. IP Address spoofing - change source IP so that it seems that the attack is coming from somewhere else
6. creating custom packets
7. randomizing host order
8. sending bad checksums - send the packets with bad or bogus TCP/UDP checksums to the intended target
9. proxy servers
10. anonymizers - use anonymizers that allow them to bypass internet censors and evade certain IDS and firewall rules

tool used
#### [[Nmap]]

```bash
nmap -f <target-ip>
```
> -f = used to split the IP packet into tiny fragments packets

```bash
nmap -g 80 <target-ip>
```
> -g = used to specify the source port
> > here the source port is changed to ''80'' which is a common port an is most generally allowed in various firewall rules
>
> --source-port = also does the same thing

```bash
nmap -mtu 8 <target-ip>
```
> -mtu = MTU (Maximum Transmission Unit)
> > smaller packets are sent/transmitted instead of sending one complete packet at a time

```bash
nmap -D RND:10 <target-ip> 
```
> -D = performs a decoy scan
> RND = to generate random IP addresses
>> here 10

```bash
nmap -sT -Pn --spoof-mac 0 <target-ip>
```
> --spoof-mac 0 = means to randomize the source MAC address

### Network scanning using Various Scanning tools

tools used
#### [[Metasploit]] #tool/linux 

```bash
msfconsole
```
> launches an interactive shell 

```
nmap -Pn -sS -A -oX Test <10.10.1.0/24>
```
> to scan for whole network range 

```
search portscan
```
> searches for modules for portscanning
> here we are using `auxiliary/scanner/portscan/syn`
> > this module performs a syn scan on the targets

```
use auxiliary/scanner/portscan/syn
```
set parameters 
```
set INTERFACE eth0
```
```
set PORTS 80
```
```
set RHOSTS 10.10.1.5-23
```
> target range , can also use [[CIDR]]
```
set THREADS 50
```
```
run
```

next we perform TCP scan 
```
use auxiliary/scanner/portscan/tcp
```
```
show options
```
> this will show different variables and parameters that can be set in the module

```
set RHOSTS <target-IP>
```
```
run
```
> in this we performed a TCP scan for open port on a single IP address as scanning multiple IP addresses consumes much time

> use `back` to revert to msf command line

```
use auxiliary/scanner/smb/smb_version
```
> we use this to determine various aspects using SMB protocol
> > if port 445 is open

```
set RHOSTS <target-IP or range>
```
```
set THREADS 11
```
```
run
```

> can also use other various modules for further information
> for eg FTP module to identify the FTP version and perform further vulnerability analysis

or in other way to perform it directly from the shell , do

```bash
msfconsole -x "use auxiliary/scanner/portscan/tcp; set RHOSTS <target-IP>; run; exit"
```
> -x = implements the commands inside of the console directly from the shell

