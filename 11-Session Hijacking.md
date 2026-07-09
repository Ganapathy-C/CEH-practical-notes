# Session Hijacking

session hijacking is when an attacker takes over either a valid TCP communication session b/w 2 computers or a valid session of a user in a web application

2 types
1. active session hijacking - finds and use an active session
2. passive session hijacking - finds a session and instead of taking over, keeps an eye on it passively

## perform Session hijacking

session hijacking can be divided into three phases

- tracking the connection - sniffing using [[nmap]], caido, burpsuite, wireshark etc.
- descync-ing the connection - 
- injecting the attackers packet - once interrupted, attacker can inject his malicious content into the network / connection

### hijack a session

tool used
#### [[caido]]
#gui #tool/windows 

used in auditing web applications

In Attacker Windows machine to reset the DNS cache -
```
ipconfig /flushdns
```

→  Run `caido.exe` or search caido and start
→ create an account with `caido`
→ `start` ≫ `Edit`
→ check `All interfaces` ≫ `start`
→ login
→ set instance name - _here **Session Hijacking**_ 
→ `Allow`

→ `+ create a project` ≫ enter a name _here **Session Hijacking**_
→ `Intercept`
→ `Forwarding` and wait until `Queuing`

In victim's machine Windows or Linux:

we add the proxy to the browser of the victim so that all the traffic flows through attacker pc ( the one containing Caido)

→ firefox -> settings -> search proxy -> manual proxy -> Ip of machine running caido and port 8080 and checkbox of https and save

Downloading and settingup the CA certificate on victim browser.
```
http://10.10.1.11:8080/ca.crt
```
→ a certificate downloads and we will add this to the browser 
→ settings page of firefox
→ search `certificate` ≫ `view certificate`
→ `Authorities` ≫ Import
→ select the `ca.crt` downloaded recently
→ check `Trust CA to identify websites`
→ search `proxy`
→ add manually - 10.10.1.11, port = 8080 and check `Also use this proxy for HTTPS`
→ save and exit
→ now browse any website  _here `www.moviescope.com`_

in the attacker pc
→ we are capturing all the traffic from the victim's browser
→ on the `requests` tab , switch all the `www.moviescope.com` requests to `www.goodshopping.com`
→ modify every GET request to `www.goodshopping.com` until u see the website in the victim pc

here, the victim tried to access `www.moviescope.com` but visited `www.goodshopping.com`
even the website url will show `www.moviescope.com` but the window displays `www.goodshopping.com`

### Intercept Or Logging HTTP the traffic

tool used
#### [[hetty]]
#gui #tool/windows 
#website 

→ run `hetty.exe` - a command prompt appears and hetty starts
→ launch any browser *here firefox*
→ navigate to open hetty dashboard
```
http://localhost:8080
```

→ `MANAGE PROJECTS` ≫ enter a name of the project *here `moviscope`*
→ this name will appear below under `Manage Projects` as *active*
→ `Proxy logs` 

now we switch to the target machine
→ open browser *here chrome*
→ settings ≫ search `proxy`
→ click `Open your computer proxy settings`
→ enter manually 10.10.1.11, port = 8080 ≫ `SAVE`
→ open browser and search `http://www.moviescope.com`
→ login using sam/test

In the attacker machine
→ we can see the logs in our hetty proxy logs
→ look for a POST request ( as most logins are POST requests )
→ in the `Body` tab under `POST /` tab we can look into more details of what is happening in the request that is being sent by the victim
→ if we scroll down we can also see the username and password of the victim in plaintext

in the end to clear out tracks
we go to the victim's proxy settings and revert back the original state

### Detect Session Hijacking

tool used
#### [[Wireshark]]
#gui #tool/windows 
 
 Open the wireshark on Victim machine with  local ethernet interface to detect the session hijacking

and
#### [[bettercap]] 
#tool/linux 

In attacker machine , we need to run bettercap to simulate the session hijacking attempt

```
bettercap -iface eth0
```
To list all the hosts on the subnet
```
net probe.on
```
To do recon, so that any new host added to the network will be added for sniffing
```
net recon.on
```
To start sniffing
```
net sniff.on
```
After all the hosts communicating request first goes to Attacker machine running bettercap and then it will moves to destination.

this will simulate that someone is trying to sniff or trying to attack the network
but this process sends alot of ARP packets
which can be detected in the Wireshark

---
