# Sniffing

## Active sniffing

Active sniffing involves sending out multiple network probes to identify access points. The following is the list of different active sniffing techniques:

- MAC Flooding: Involves flooding the CAM table with fake MAC address and IP pairs until it is full
- DNS Poisoning: Involves tricking a DNS server into believing that it has received authentic information when, in reality, it has not
- ARP Poisoning: Involves constructing a large number of forged ARP request and reply packets to overload a switch
- DHCP Attacks: Involves performing a DHCP starvation attack and a rogue DHCP server attack
- Switch port stealing: Involves flooding the switch with forged gratuitous ARP packets with the target MAC address as the source
- Spoofing Attack: Involves performing MAC spoofing, VLAN hopping, and STP attacks to steal sensitive information
### mac flooding 

→ open [[Wireshark]] in the target machine to view the flow of packets

tool used
#### macof
#tool/linux 

> mac flooding is a technique to force a switch to act as a hub
> floods the CAM table with random MAC address and ip addresses
> roughly 131000 entries per minute
> when MAC table fills up, switch converts to a hub-like operation where an attacker can monitor the data being broadcasted


```bash
macof -i eth0 -n 10
```
> -i = interface
> > here eth0
> 
> -n = number of packets

or if u want to target a single ip
```bash
macof -i eth0 -d <target-ip>
```
> -d = specifies destination


### DHCP starvation

> attacker floods the DHCP server by sending large number of DHCP requests and uses up all the ip addresses thus allotted
> this results in DOS attack

→ open [[wireshark]] in the background to view the flow of packets during the attack
tool used
#### [[yersinia]]
#tool/linux 

> this tool has an interactive shell
> and only works when terminal is maximized

```bash
yersinia -I
```

→ press `h` for help
→ list of `available commands` show up
→ press `q` to exit help menu
→ `F2` to switch to DHCP mode
> STP fields change to DHCP fields

→ `x` to list attack options
→ `1` to start DHCP starvation attack
→ `q` to stop

## network sniffing 

tool used
#### [[Wireshark]]
#tool/windows #gui 

→ open wireshark
→ select interface 
> here ethernet 2

→ go to any http website
> here http://www.moviescope.com/

→ login via credentials
→ switch to wireshark
→ stop capturing
→ apply filter `http.request.method==POST`
→ edit → find packet 
> > display filter = string
> > narrow & wide -> Narrow(UTF-8 / ASCII)
> > packet list -> packet details
> > field next to string -> `pwd`

→ select the packet
→ look within the details (*HTML Form URL Encoded: application/x-www-form-urlencoded*)
→ username and password in plaintext

#### [[Wireshark]] for remote sniffing

→ login to victim pc
> here Jason/qwerty

→ control panel ≫ system and security ≫ windows tools ≫ services
OR
→ search `services` in the home menu and open

→ right click on **Remote Packet Capture Protocol v.0(experimental)** and `start`
→ back to attacker machine ≫ capture options (from toolbar)
→ manage interfaces
→ remote interfaces
→ "+" sign at the bottom
→ host = target machine ip
→ port = 2002
→ password authentication = Jason/qwerty ≫ OK ≫OK
→ new remote interface will show and wireshark will start capturing in 2 interfaces

now we can sniff packets remotely


## Detect Network Sniffing


### detect ARP poisoning 

ARP poisoning = forging many ARP requests and reply packets to overload the switch
ARP cache poisoning = method of attacking the LAN network by updating the target computers ARP cache 

here we use 
#### [[Cain and Abel]] 
> to poison the traffic

→ launch `Cain and Abel` 
→ in configure dialogue = check adaptor and IP address association
→ `Start/Stop Sniffer`
→ `Sniffer` tab
→ "+" icon to scan MAC address
→ check all hosts in my subnet
→ and select all tests
> it will scan for MAC address and list all those found 

→ `APR` (bottom)
→ click on the top half of the pane
→ APR options appear on the left
→ click on the right pane to activate the "+" icon
→ click "+" 
→ New ARP routing
→ select the targets between which we want to perform ARP poisoning ≫ OK
→ `Start/Stop APR`

simulate traffic between the target machines selected above
```bash
hping3 <target-ip> -c 1000000
```

→ wireshark 
→ Edit
→ preferences…
→ expand protocols
→ select ARP/RARP
→ in the right pane select all 
→ start capturing

→ in the cain and abel window - packet flowing can be seen

→ in the wireshark
→ stop packet capturing
→ analyze
→ expert information
→ duplicate IP address configured can be seen
→ highlighted in yellow - indicates that duplicate IP address have been detected at one MAC address

### detect Promiscuous mode

tool used
#### [[nmap]]

```bash
nmap --script=sniffer-detect <target-ip/range>
```

---
