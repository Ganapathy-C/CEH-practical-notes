# Denial-of-Service

Dos = Denial of service
DDoS = Distributed denial of service

attacks one of the CIA triad directly
i.e. availability

works on a simple logic
> overwhelm the target resources so that it stops working 
> thus hindering the availability of resources

mainly aim at the bandwidth: they exhaust network, application, or service resources and thereby restrict legitimate users from accessing the system or the resources of the system/network

3 categories of DoS and DDoS attacks

1. Volumetric - consume bandwidth
	1. UDP Flood
	2. ICMP Flood
	3. Ping of Death
	4. Zero Day
2. protocol attacks - consume resources in components such as firewalls, load-balancers etc.
	1. SYN Flood
	2. Fragmentation Attack
	3. Spoofed Session attack
	4. ACK Flood attack
3. Application layer - consume application resource
	1. HTTP GET/HTTP POST attack
	2. Slowloris Attack
	3. UDP application Layer attack
	4. DDoS extortion attack


## perform DDoS attack

this can be done legitimately for the stress-testing of the server or the system

tool used

#### ISB
#tool/windows #gui 
ISB = I'm so bored
it can perform various attacks
- HTTP flood
- UDP FLood
- TCP Flood
- TCP Port scan
- ICMP Flood
- Slowloris
or we can use this to gather information on the target using
- [[whois]]
- NS
- [[traceroute]]
- Browser
- ping 

> in the lab we are using it to perform a TCP flood

→ run `I'm so Bored.exe`
→ set target IP and port
→ select TCP flood
> set parameters interval = 10 , buffer = 256 , threads = 1000

→ `Start Attack`

#### UltraDDoS

→ open `ultraddos.exe`
→ a command prompt like window appears
→ click on `DDOS attack`
→ enter your target
→ define the port 
> here 80

→ define no. of packets *more is better, but too many will crash your computer*
→ define the no. of threads *can be same as n. of packets*
→ `OK` to start the attack

IF u have access to the victim machine u can check the resources in **Resmon**

### using Botnets

In the lab the use of botnets is done by getting the reverse shell on different targets
transferring the `eagle-dos.py` to all the different targets using

```bash
upload /home/attacker/Downloads/eagle-dos.py
```

and then running the python script using
```
python eagle-dos.py
```

## Detect and Protect 

as much as stress testing a server or a system is necessary
it is as much as important to detect and protect your system against a DoS attack

#### LOIC
#tool/windows #gui 
LOIC = low Orbit Ion Cannon

open-source network stress testing and denial-of-service (DoS) attack application
very famous and easy to use
can be used via a mobile phone

→ open `LOIC.exe`
→ Enter IP or URL of the target
→ in the Attack options 
> > change to UDP 
> > threads = 5
> > and roughly in the middle of speed
> > u can change other parameters as per the need
> 
> `IMMA CHARGIN MAH LAZER` to initiate the attack
> `Stop Flooding` to stop the attack

#### Anti DDoS Guardian
#tool/windows #gui 

it is a tool that detects unusual and extra traffic on the target
u can also use this to block the attack

> after initiating the attack from the attacker
> we can see that their are an unusual number of high packets flowing into the victim machine from specific targets
> `double click` any target and we get several option to choose from
> - clear
> - stop listing
> - block IP
> - allow IP

> in the lab we block the IP

---