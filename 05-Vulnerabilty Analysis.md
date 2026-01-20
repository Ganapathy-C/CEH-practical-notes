# Vulnerabilty Analysis

is an examination of the ability of a system or application , including current security procedures and controls , to withstand an assault

## CWE
#website 

> CWE - Common Weakness Enumeration
 
https://cwe.mitre.org/ 

> it is a category system for software vulnerabilities an weakness
> mostly **used as a baseline** for weakness identification , mitigation , and prevention efforts.
> also has an advanced search which you can use for research an concepts

## Vulnerability assessment tools

#### [[OpenVas]]
#tool/linux  #docker #website 

> framework of several services an tools that offer a comprehensive an powerful vulnerability scanner and management tool
> scanner is accompanied by an update feed of Network Vulnerability Tests (NVTs) - over 50,000 tests


```bash
docker run -d -p 443:443 -name openvas mikesplain/openvas
```
→ open firefox
→ https://127.0.0.1/ → login with admin/admin
→ Scans → Tasks → (hover over the wand) Task Wizard
→ target IP → wait for the scan to finish

#### [[nikto]]
#tool/linux 

```bash
nikto -h <target-url>
```

#### [[nmap]]

```bash
nmap --script http-vuln* -p 80 <target-url>
```

#### [[skipfish]]
#tool/linux 

```bash
skipfish -o <output-filename> <target-url>
```

---
