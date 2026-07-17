# SQL Injection

tool used
#### [[sqlmap]]

#tool/linux 

Sqlmap is an open-source penetration testing tool that automates the process of detecting and exploiting SQL injection flaws and taking over of database servers. It comes with a powerful detection engine, many niche features, and a broad range of switches-from database fingerprinting and data fetching from the database to accessing the underlying file system and executing commands on the OS via out-of-band connections.

You can use sqlmap to perform SQL injection on a target website using various techniques, including Boolean-based blind, time-based blind, error-based, UNION query-based, stacked queries, and out-of-band SQL injection.

> sqlmap requires cookies to work better
> if we do not provide any cookies it'll work as a outsider only
> once cookies are provided it can search deeper and with more precision

to find the cookies
1. open the website **http://www.moviescope.com/**. A Login page loads; enter the Username and Password as sam and test, respectively. Click the Login button.
2. login *this page's URL is also necessary as this is the URL we'll be using in the command so we can copy it*
3. Once you are logged into the website, **click the View Profile tab** on the menu bar and, when the page has loaded, make a note of the URL in the address bar of the browser.
4. __right-click__ anywhere on the page and press __inspect__
5. in the __console__ tab write `document.cookie`
6. copy the whole line including the \""


```bash
sqlmap -u <target url with paramter> --cookie <cookie> --dbs
```

> --dbs = is to enumerate dbms databases

once databases are enumerated we need to enumerate tables

```bash
sqlmap -u <target url with paramter> --cookie <cookie> -D <database name> --tables
```

> --tables = enumerate the tables in the database provided

once we have the tables too, we can simply data dump the whole table

```bash
sqlmap -u <target url with paramter> --cookie <cookie> -D <database name> -T <table name> --dump
```

no sql injection attacks can also provide one with an interactive shell too
if thats the case 
we can try

```bash
sqlmap -u <target url with paramter> --cookie <cookie> --os-shell
```
→ type `help` to view the available commands and Type TASKLIST and press Enter to view a list of tasks that are currently running on the target system

#### other SQL injection tools
- Mole - https://sourceforge.net
- jSQL
- NoSQLmap
- Havij
- blind-sql-bitshifting

---

## Detect SQL Injection Vulnerabilities

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

#### other SQL injection detection tools
- **Damn Small SQLi Scanner** (**DSSS**) (https://github.com), 
- [[Snort]] (https://snort.org), 
- **[[BurpSuite]]** (https://www.portswigger.net), 
- **HCL AppScan** (https://www. hcl-software.com) etc. to detect SQL injection vulnerabilities.

---

