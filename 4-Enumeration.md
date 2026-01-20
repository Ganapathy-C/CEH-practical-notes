# Enumeration

the process of extracting usernames, machine names, network resources , shares , and services from a system of or network

## [[NetBIOS]] Enumeration

tool used
#### [[nbtstat]]
#tool/windows 

> helps in troubleshooting NETBIOS name resolution problems
> removes an corrects preloaded entries using several case-sensitive switches
> can be use to enumerate information
> > 1. NetBIOS over TCP/IP (NetBT) protocol stats
> > 2. NetBIOS tables for both the local and remote computers
> > 3. NetBIOS name cache 


```cmd
nbtstat -a <IP-address of the remote machine>
```
> -a = displays the NetBIOS name table of the remote computer

```cmd
nbtstat -c
```
> -c = lists the content of the NetBIOS name cache of the remote computer
> > it is possible to get this info without even creating a *null session* ( an unauthenticated session )

```cmd
net use
```
> this output displays information about the target such as connection status , shared folder /drive and network information

## [[SNMP]] Enumeration

this helps to extract information about the 
>1. network resources
> > 1. hosts
> > 2. routers
> > 3. devices
> > 4. shares 
>2. network information
> > 1. ARP tables
> > 2. routing tables
> > 3. device specific info
> > 4. traffic stats

tool used 
#### [[snmpwalk]]
#tool/linux 

> Cli tool that scans numerous SNMP nodes instantly and identifies a set of variables that are available for accessing the target network


```bash
snmpwalk -v1 -c public <target-IP>
```
> -v = specifies snmp version to be used
> > 1 , 2c , 3 
>
> -c = stands for community string

```bash
snmpwalk -v2c -c public <target-IP>
```

> results display data transmitted from the SNMP agent to the server , including information on server, user credentials , and other parameters

## [[LDAP]] Enumeration

tool used 
#### AD Explorer
#tool/windows #gui 

> it is an advanced AD Viewer and Editor
>  can be used to 
> > 1. navigate AD databases easily 
> > 2. define favorite locations 
> > 3. view objects properties and attributes without opening dialogue boxes
> > 4. edit permissions
> > 5. view an object's schema
> > 6. execute sophisticated searches that can be saved and re-executed


→ connect to : <"target-IP">
→ expand columns on the left pane to get more detailed information
→ right-click on any attribute to modify or view `properties`

#### other tools for LDAP enumeration

- Softerra LDAP Administrator (https://www.ldapadministrator.com)
- LDAP Admin tool (https://www.ldapsoft.com)
- LDAP Account Manager (https://www.ldap-account-manager.org)
- LDAP Search (https://securityxploded.com)

## [[NFS]] Enumeration

tools used 
#### [[RPCScan]] 
#tool/linux 

> RPC = Remote Procedural Call
> this is the services by which RPCScan communicates
> checks NFS shares misconfiguration
> it lists 
> > 1. RPC services
> > 2. mount points
> > 3. directories accessible via NFS
> > 4. can also list NFS shares recursively

to check if NFS services are running on the target machine
```bash
nmap -p 2049 <target-IP>
```
> -p = for port specification
> > 2049 is default port for NFS

```bash
cd RPCScan
```
```bash
python3 rpc-scan.py <target-IP> --rpc
```
> --rpc = lists the RPC (portmapper)

#### [[SuperEnum]]
#tool/linux 

> another script that does the basic enumeration on any port including RPC (2049)

```bash
cd SuperEnum
```
```bash
./superenum
```
> superenum requires a file that has the list of IP's in it
> so create a file before `./superenum`

```bash
echo <target-IP> > target.txt
```

*if there is an error running the script*
```bash
chmod +x superenum
```

## DNS Enumeration

performed using technique **Zone Transfer**

#### [[dig]]
#tool/linux 
 
```bash
dig ns <target-domain>
```
> ns = returns the name server in the result
> > displays in the **ANSWER SECTION** 
> 
> > on linux systems `dig` command is used to retrieve information about the target host address , name servers , mail exchanges etc.
> 
> 

```bash
dif @<name-server> <target-domain> axfr
```
> axfr = retrieves zone information

> if this fails that means that the DNS zone transfers are not allowed
> > output will be **Transfer failed**

other tool
#### [[nslookup]]
#tool/windows 

```
nslookup
```
```
set type=soa
```
> soa = Start of Authority
> > record to retrieve administrative information about the DNS zone of the target domain
> > results shows - primary name server and responsible mail address

within the interactive shell
```
ls -d <name-server>
```
> this will request a zone transfer of the specified name server

## [[SMTP]] Enumeration

tool used

#### [[Nmap]]
#tool/linux 

> ```bash
> nmap -p 25 --script=smtp-enum-users <target-IP>
> ```
> results show list of all the possible mail users on the target machine


> ```bash
>nmap -p 25 --scrIPt=smtp-open-relay
>```
> results show a list of open SMTP relays on the target machine

>```bash
>nmap -p 25 --script=smtp-commands
>```
> results show all the SMTP commands available in the nmap directory appears

## Enumeration Tools

#### Global Network Inventory
#tool/windows #gui 

→ in the Audit Scan Mode - `Single Address Scan`
> can also scan an entire IP range - `IP range scan`

→ in Single Address Scan - <'target-IP'>
→ Connect as - type admin name an password
> IRL these is left on `Connect as currently logged on user`
> > but this does not provide much info 

→ leave rest to Default and click `Finish`
→ click on various tabs to get information about the target



## SMB 

its like FTP for Windows
#### [[smbclient]]
it lets u connect to a window share from a linux device

to connect
```bash
smbclient //<ip>/<name of the share> -U <username>
```

→ to list the available shares on the device
```bash
smbclient -L //<IP>
```
> this will list all the available shares that can be accessed (may require password)

→ Anonymous / null session (very common in pentesting)
```bash
smbclient -L //172.30.10.200 -N
```
> -N → no password

but if we have password
```bash
smbclient //<ip>/name of the share/
```
> prompt ID and Pass
> and we have a shell

commands that can be used inside the shell

| Command        | Meaning                |
| -------------- | ---------------------- |
| `ls`           | List files             |
| `cd folder`    | Change directory       |
| `pwd`          | Show current directory |
| `get file.txt` | Download file          |
| `put file.txt` | Upload file            |
| `mkdir dir`    | Create directory       |
| `del file.txt` | Delete file            |
| `exit`         | Quit                   |



---