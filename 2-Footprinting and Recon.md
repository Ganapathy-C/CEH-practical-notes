**Alan Hollinghurst** – honored for novels like _The Line of Beauty_, _Our Evenings_# reconnaissance and footprinting

- **Active Footprinting** – actively probing the target network for live data.  
- **Passive Footprinting** – gathering information from publicly available sources.

## advanced google hacking techniques

### [[Google Dorking]]


| Operator       | Description                                           |
| -------------- | ----------------------------------------------------- |
| `intitle:`     | Search for a keyword in the title of a page           |
| `site:`        | Limit results to a specific site                      |
| `filetype:`    | Restrict search to a particular file type             |
| `cache:`       | Find cached versions of a site                        |
| `allinurl:`    | Look for all keywords in the URL                      |
| `inurl:`       | Search for a keyword in the URL                       |
| `allintitle:`  | Show pages whose titles contain all listed words      |
| `intitle:`     | Same as above but with fewer terms                    |
| `inanchor:`    | Match anchors containing the keyword                  |
| `allinanchor:` | Same as above, broader match                          |
| `link:`        | Find a link to the homepage or other page of the site |
| `related:`     | Show sites similar to the one provided                |
| `info:`        | Retrieve information about a specific webpage         |
| `location:`    | Locate the hosting location (e.g., HQ)                |

## initial recon

#### www.sitereport.netcraft.com
#website 
- tech-stack detection an various other information regarding the website
> background, Network, Hosting History

#### https://dnsdumspter.com
#website 
- GEOIP of the host locations
> NS servers, MX records, Host Records (A)
> Domain mapping can also be seen 

- Pentest-Tools Find Subdomains (https://pentesttools.com) - identify the domains and subdomains of any target


## Information gathering from various social networking sites
tools used 
#### [[sherlock]]
#tool/linux

```bash
sherlock "<target name>"
```

#### other tools 
- Social Searcher (https://www.social-searcher.com)


## whois footprinting

>gather info about the IP address and domain

 >whois is a query an response protocol used for querying databases that store the registered users or assignees of an Internet resource such as domain name, an IP address block, or an autonomous system.

#### https://whois.domainstools.com
#website

#### other tools
- SmartWhois (https://www.tamos.com)
- Batch IP Converter (http://www.sabsoft.com)


## DNS footprinting

DNS is considered the intermediary source of any internet communications.
primary function include the translation of IP address to domain name and vice versa

tools used
#### [[nslookup]]
#tool/windows  

```
nslookup
``` 
> starts the tool in an interactive mode

```
set type=a
```
> "a" configures to the query for IP address of the given domain
>after the type has been set, provide the name of the target

> this provides a non-authoritative answer

```
set type=cname
```
> lists cname records for the domain. its done directly against the domains authoritative name server

> type the name of the target domain again

> this returns the domain's authoritative name server

now all we need to do is find the IP address of name server
```
set type=a
```
→ then type name of the name server

- nslookup website #tool  - (http://www.kloth.net/services/nslookup.php)
> only need to type the name of the target domain and query type

## Network footprinting

accumulating data regarding a specific network environment

tool used 
#### [[tracert]]
#tool/windows 
> shows the hops from the client to the server - their number and ip address

```
tracert /?
```
>shows the help for the command
```
tracert -h 5 "<target-domain>"
```
> -h = maximum number of hops

#### [[traceroute]]
#tool/linux 
> views the hops made before reaching the destination
```bash
traceroute "<target-domain>"
```

#### other tools
- PingPlotter (https://www.pingplotter.com/)
- Traceroute NG (https://www.solarwinds.com)

## email Footprinting

tool used
#### [[eMailTrackerPro]] 
#tool/windows #gui 

→ My Trace Reports

→ Trace Headers

→ trace an email i have received
> _to find a email headers click `show original` from the three dots menu in an email_

→ Trace
> a world map shows the location of the mail
> further details can be found in the email summary tab
> > _this includes Network whois, Domain whois etc_



#### other tools
- MxToolBox (https://mxtoolbox.com)
- Social Catfish (https://socialcatfish.com/)
- IP2Location Email Header Tracer (https://www.ip2location.com/)

## Footprinting using tools

#### [[Recon-ng]]
#tool/linux 

```bash
recon-ng
```
```
help
```
> shows help
```
marketplace install all
```
> marketplace is the place or tab where all the modules are located that can be used inside of recon-ng

```
module search
```
> displays all the modules available in recon-ng

we can perform Network discovery, Exploitation, Reconnaissance etc. by loading different modules

```
workspaces
```
> manage different workspaces with this command
```
workspace create <workspace-name>
```
```
workspace list
```

##### domain/Host discovery

```
db insert domains
```
> to set the target domain
> type the name of the target in the next line

```
modules load brute
```
> shows all the modules related to bruteforcing

here we are using the module `recon/domains-hosts/brute_hosts` - to harvest the hosts
```
modules load recon/domains-hosts/brute_hosts
```
```
run
```
> this will run the loaded module on the domain listed before

```
back
```
```
modules load recon/domain-hosts/bing_domain-web
```
```
run
```

```
modules load recon/hosts-hosts/reverse_resolve
```
```
run
```
 once all this is done issue the command 
 ```
 show hosts
 ```
> this will show all the hosts harvested so far

##### to create a report 

use `back` to go to the workspaces terminal
```
modules load reporting/html
```
to change the various aspects of the generated report
```
options set FILENAME <path/to/filename>
```
```
options set CREATOR <your-name>
```
```
options set CUSTOMER <customer-name>
```
```
run
```

##### Recon-ng for Reconnaissance

```
workspace create reconnaissance
```
```
modules load recon/domains-contacts/whois_pocs
```
>uses ARIN Whois RWS to harvest POC data

```
info command
```

```
options set SOURCE <target-domain>
```
```
run
```

##### Recon-ng to extract list of subdomains and IP addresses associated with the target URL
```
modules load recon/domains-hosts/hackertarget
```
```
options set SOURCE <target-domain>
```
```
run
```

---