# Iot and OT Hacking

### Footprinting

 first step is to extract 
 - IP address
 - protocol used 
	 - MQTT
	 - ModBus
	 - ZigBee
	 - BLE
	 - 5G
	 - IPv6LoWPAN
- open port
- device type
- geolocation
- manufacturing number
- manufacturer
many other information including
- Hostname
- ISP
- Banner of the target IoT device
- FCC ID information
- Certification granted to the device

### using online tools

tool used
#### [[Whois]]
#website 

 - https://whois.domaintools.com
 - https://www.whois.com
here we will be looking for `www.oasis-open.org`
> it is an organization that has published MQTT v5.0, which powers most of the IoT in the world 


#### Exploit-DB
#website 

its a database of collection of all the vulnerabilities found and documented 

> - https://www.exploit-db.com/google-hacking-databse

→ in the main window, search `SCADA`

#### [[Google Dorking]] 
#website 

→ in the google search bar
→ type `"login" intitle:"scada login"`
→ it'll list all the pages that are SCADA login webpages

#### [[Shodan]]
#website 

> https://account.shodan.io/login

→ login into the website
→ search for `port:1833`
> port 1833 is the default MQTT port defined by IANA as **MQTT over TCP**
- we can get info like
	- IP Address
	- Ports
	- Hostnames
	- ASN etc.

more shodan searches

> ModBus enabled ICS/SCADA systems
- `port:502`

> SCADA systems using PLC name
- `"Schneider Electric"`

> SCADA systems using geolocation
- `SCADA country:"US"`


## Capture and Analyze IoT device


> in the lab we simulate this traffic using **Bevywise IoT Simulator**
> check lab manual for more info

tool used
#### [[Wireshark]]
#tool/linux #tool/windows #gui 

→ start capturing the traffic on the interface
→ in the search bar filter by `MQTT`
→ the IoT traffic gets filtered out
- **MQTT specifics** – After a successful connection to an MQTT broker, the client can publish messages. The packet headers include:  
  * Header Flags, DUP flag, QoS, Retain Flag, Topic Name, Message, Payload.  
  * A PUBREL packet replies to a PUBREC, and a PUBCOMP packet completes the exchange.
→ analyze the packet for more information
> check **Publish ACK**, **Publish Received**, **Publish Release**, **Publish Complete**

After establishing a successful connection with the MQTT broker, the MQTT client can publish messages. The headers in the Publish Message packet are given below:

- Header Flags: Contains information regarding the MQTT control packet type.
- DUP flag: If the DUP flag is 0, it indicates the first attempt at sending this PUBLISH packet; if the flag is 1, it indicates a possible re-attempt at sending the message.
- QoS: Determines the assurance level of a message.
- Retain Flag: If the retain flag is set to 1, the server must store the message and its QoS, so it can cater to future subscriptions matching the topic.
- Topic Name: Contains a UTF-8 string that can also include forward slashes when it needs to be hierarchically structured.
- Message: Contains the actual data to be transmitted.
- Payload: Contains the message that is being published.

→ details can be found under `MQ Telemetry Transport Protocol` (**MQTT Protocol**)


## Perform IoT attack

### Replay Attack the CAN Protocol

→ refer to the Manual, better explained there

---
