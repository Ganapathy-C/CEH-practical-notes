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

**WampServer Setting In Victim**:

Note: In this task, the **target** WordPress website (**http://10.10.1.22:8080/CEH**) is hosted by the **victim** machine, Windows Server 2022. 

Here, the host machine is the Parrot Security machine. Note: **Ensure** that the **Wampserver** is **running** in Windows Server 2022 machine. To run the WampServer, execute the following steps:

  -> Turn on the **Windows Server 2022**, click Ctrl+Alt+Delete to activate the machine and login with CEH\Administrator / Pa$$w0rd.
  -> Now, click Type here to search field on the Desktop, search for wampserver64 in the search bar and select Wampserver64 from the results.
  -> Click the Show hidden icons icon, observe that the WampServer icon appears. 
  -> Wait for this icon to turn green, which indicates that the WampServer is successfully running.

1. Turn on the **Parrot Security** virtual machine, login using attacker/toor.
2. Launch the Mozilla Firefox web browser and go to **http://10.10.1.22:8080/CEH/wp-login.php?**.
   **Note**: Here, we will perform a brute-force attack on the designated WordPress website hosted by the Windows Server 2022 machine.
   

the admin panel of the Wordpress is notoriously famous for its username enum issue
in this we try to bruteforce the admin panel

3. Do this on firefox for this add to your proxy
→ 127.0.0.1
→ port = 8080
→ and check for the **also use this for HTTPS**

or u can open the burp browser to perform the attack or firefox , hit the url and capture the request on proxy and move it to intruder
-> perform the attack using intruder
-> Navigate to the Payloads tab under the Intruder tab and ensure that under the Payload Sets section, the Payload set is selected as 1, and the Payload type is selected as Simple list.
 
-> Under the **Payload settings** [Simple list] section, click the Load... button. 
-> A file selection window appears; navigate to the location **/home/attacker/Desktop/CEHv13 Module 14 Hacking Web Applications/Wordlist**, select the **username.txt** file and **password.txt** and click the Open button.


look for any different **response code** or change in response **length**

---

### Perform RCE in a vulnerable WordPress plugin

tool used
#### [[WPScan]]

Here, we will perform a CSRF attack using vulnerability present in the wp-upg plugin. 

1. Switch to the **Windows Server 2022** machine. Click Type here to search field on the Desktop, search for wampserver64 in the search bar and select **Wampserver64** from the results.
2. Now, in the right corner of **Desktop**, click the **Show hidden icons** icon, observe that the **WampServer** icon appears.
3. Wait for this icon to turn green, which indicates that the WampServer is successfully running.
4.  Now, **open any web browser**, and go to **http://10.10.1.22:8080/CEH/wp-login.php**? (here, we are using Mozilla Firefox).
     Note: Here, we are opening the above-mentioned website as the victim.
5. A WordPress webpage appears. Type Username or Email Address and Password as **admin** and **qwerty@123**. Click the Log In button.
6. Assume that you have installed and configured **User Post Gallery plugin**
7. Hover your mouse cursor on Plugins in the left pane and click Installed Plugins, as shown in the screenshot.
8. In the Plugins page, observe that User Post Gallery is installed. **Click Activate** under the **User Post Gallery** plugin to activate the plugin
9.  Switch to the **Parrot Security** machine.
10. Open Mozilla Firefox web browser and go to **https://wpscan.com/** and login to the **wpscan account** that you have created in previous task.
11. You get signed in successfully in the website. Now, click the Get Started button and click **Start for free button under Researcher section**.
12. The **Edit Profile pag**e appears; in the **API Token** section and observe the **API Token**. Note **down or copy this API Token**; we will use this token in the later step
13. Close the Firefox browser window.
14. In the **Parrot Security machine**, open a Terminal window and execute sudo su to run the programs as a root user (When prompted, enter the password toor).
15. Now, run cd command to jump to the root directory.
16. In the Terminal window, run **wpscan --url http://10.10.1.22:8080/CEH --api-token API_token_from_Step#12**command.
```bash
wpscan --url <target-URl/IP> --api-token <api-token-copied> 
```
17. it'll scan the website for plugins and if they are vulnerable 
18. *in lab* we see that we found a plugin **wp-upg** that has an **unauthenticated RCE**

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
