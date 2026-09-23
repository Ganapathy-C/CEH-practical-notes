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


## Simulating IOT Traffic using BevyWise IOT Simulator , Capture and Analyze IoT Traffic using Wireshark


> in the lab we simulate this traffic using **Bevywise IoT Simulator**
> check lab manual for more info

-> On one Windows **machine1**, run the **Bevywise_MQTTRoute**.exe which will acts as Broker for messages being published.

-> On  Another windows **machine2**, run the **Bevywise_IOTSimulator**.exe , where we will create a network with name and description and will **give IP of machine1 (Broker)** and add the IOT device and will subscribe to the topic.

	->. Create a IOT network give an name, id, description
	
    ->. Add a new device with name and ID and start the device by clicking the red button at the left top corner, red color will change to green once it was connected to Broker
	
	->. click on device and subscribe to the topic and give a QOS
	
	->. open the wireshark and run on the local network to capture the MQTT traffic or message after being published to device.

-> On Windows **machine1** , open the browser and run **http://localhost:8000** with **admin:admin**

	->. We can able to find the devices that is connected
	
	->. click on the device and select the topic and write the message and click on send.

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

Here, we are using the ICSim tool to simulate CAN protocol and demonstrate how attackers sniff the transmitted packets and perform replay attack to gain basic control over the target.

-> Switch to the **Ubuntu** machine and login with **Ubuntu/toor**. 

-> In the Ubuntu machine, open a Terminal window and execute **sudo su** to run the programs as a root user (When prompted, enter the password toor).

-> Run **sudo apt-get install can-utils** to install CAN utility. Note: While installing if prompted Do you want to continue?, type Y and press Enter.

-> Now, to setup a virtual CAN interface issue following commands:
```bash
	 sudo modprobe can
	 sudo modprobe vcan
	 sudo ip link add dev vcan0 type vcan
	 sudo ip link set up vcan0
```
-> To check whether Virtual CAN interface is setup successfully, run ifconfig. Here, vcan0 interface is present which confirms that our Virtual CAN interface is setup successfully.

-> Run **chmod -R 777 ICSim** to give permissions to the ICSim folder.

-> Now, run **cd ICSim** to navigate to ICSim directory and execute make command to create two executable files for IC Simulator and CANBus Control Panel.

-> Run **./icsim vcan0** to start the ICSim simulator. You will see the IC Simulator interface as shown in the screenshot.

-> **Open a new terminal tab** and execute **sudo su** to run the programs as a root user (When prompted, enter the password toor). Navigate to ICSim directory to do so run **cd ICSim/**.

-> Execute** ./controls vcan0** to start the CANBus Control Panel. You will see the CANBus Control Panel interface as shown in the screenshot.

-> Now, we will **start sniffer to capture the traffic** sent to the ICSim Simulator by CANBus control panel simulator. To do so, **open a new terminal** tab and execute **sudo su** to run the programs as a root user (When prompted, enter the password toor). Navigate to ICSim directory to do so run **cd ICSim/**.

-> Execute **cansniffer -c vcan0** to start sniffing on the vcan0 interface. Leave this sniffer on.

-> **Open a new terminal** and execute **sudo su** to run the programs as a root user (When prompted, enter the password toor). Navigate to ICSim directory to do so run **cd ICSim/**. To capture the logs run **candump -l vcan0**.

-> After **starting to capture the logs**, open ICSim and Controller simulator and perform functions such as acceleration, turning left/right, opening and locking doors so that logs are generated. Once you are done, terminate the ongoing process by pressing **Ctrl + C**

<img width="852" height="332" alt="image" src="https://github.com/user-attachments/assets/aa3eff31-cb7b-46a0-93e1-102f2c894955" />

->  Now verify if you have obtained the **log file by executing ls command**. The .log file has been generated as shown in the screenshot.

->  Now, **to perform replay attack**, run **canplayer -I candump-2024-05-07_063502.log** and press enter. Note: Once the log file is executed, you can see the movements that were performed while creating the log file in real time in IC Simulator and CANBus control panel simulator. Note: The log file name might vary while performing lab.
