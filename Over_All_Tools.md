ping
ping 12.32.23.23
whois
whois 12.32.23.23
dig
dig 12.32.23.23
ssh
ssh user@ip -i id_rsa
-i --> optional command if we are using private key to login
scp
scp user@ip:file_with_path destination_dir
scp user@10.12.23.23:/home/user/id_rsa .
enum4linux
Enumeration tool to enumerate smb service
enum4linux -a ip
enum4linux -a 12.23.42.43 
         -a --> To enumerate all user, shares, ...
smbclient
smbclient -L -N -I 10.10.128.80
smbclient -I 10.10.128.80 -L -N   
       -I --> IP 
       -L --> to list shares 
       -N --> don't ask password
smbclient  //10.10.128.80/Anonymous -- to connect with share
NFS
Enumerate NFS Directory:
showmount -e ip
create a new dir to mount the nfs dir
mkdir /tmp/newDir
mount the directory from the nfs
mount 10.2.32.23:/shareDir /tmp/newDir
mount -o rw,ver=2 10.2.32.23:/shareDir /tmp/newDir
searchsploit
searchsploit serviceName 
searchsploit serviceversion
searchsploit -m exploitNo 
         -m --> download exploit on our local machine 
searchsploit -e  exploitNo 
          -e or --examine --> open the exploit code on our machine 
searchsploit --nmap nmapoutput.xml
          --nmap --> Giving nmap output
wifite
wifite --wpa --kill --dict rockyou.txt
wifite --wpa --dict rockyou.txt --kill
wifite - based on aircrack ng tools suite
wifite --wpa --dict wordfile --kill
aircrack-ng
airmon-ng check 
airmon-ng check kill
airmon-ng wlan0 start 
airmon-ng wlan0 stop
airodump-ng wlan0mon --list all AP 
airodump-ng wlan0mon --bssid bssid --list device connected in AP 
airodump-ng wlan0mon --bssid bssid -c chNo -w airCat --capture the wpa 4 way handshake while deauth
aireplay-ng wlan0mon -a ap_bssid --deauth deauthCount ,-c client_bssid --> also we specify
aircrack-ng captureFile -w Passwordfile , -b bssid
Hashcat to crack wpa2
systemctl stop NetworkManager.service 
systemctl stop wpa_supplicant.service
hcxdumptool -i wlan0 -o wapcap.pcapng --active_beacon --enable_status=1
hcxdumptool -i wlan0 --do_rcascan -- display AP with BSSID OR MAC
systemctl start NetworkManager.service 
systemctl start wpa_supplicant.servivce
hcxpcapngtool wapcap.pcapng -o wapcap.hc22000 -E essidList
From above hc22000 file find the required BSSID and remove others
use hashcat below
Hashcat
we have to give only the hashes, if we had given username with password hash need to use --username flag
hashcat -m hashtype -a attacktype hashFile wordfile_brute --username
hashcat -m hashtype -a attacktype hashFile wordfile_bruteforceFormat
hashcat -m 22000 wpa.hc22000 /usr/share/wordlist/rockyou.txt
hashcat -m 22000 wpa.hc22000 -a 3 ?d?d?d?d?d?d?d?d
hashcat -m 22000 wpa.hc22000 -a 3 Password@?d?d?d
hashcat -m 22000 wpa.hc22000 -a 3 -1 ?d?l?u ?1?1?1?1?1?1?1?1
hashcat -m 22000 wpa.hc22000 -a 3 --increment --increment-min 8 --increment-max 10 ?d?d?d?d?d?d?d?d?d?d
John 
we can give hash with username separated by colon(root:$6$laslxsaa24923)
john --wordlist=/usr/share/wordlists/rockyou.txt hash --format=sha512crypt
john -w=/usr/share/wordlists/rockyou.txt hash
john --list=formats
john hashFileName --show --> to show cracked password
hydra
hydra -l username -p password ip service name -s 23
     -l --> login 
     -p --> password 
     -s --> to specify port if service not running in default port
hydra -L usernameList -P passwordList ip service name -t 64   ___ max threads 64
     -L --> Username List
     -P --> Password List 
     -t --> threads per target (MAX:64)
gobuster - without thread is good
gobuster dir -u url -w wordfile -t 1000 -o outputfi -k -x html,php,js,config,config
-k   --> Skip SSL certificate verification
-x   --> Extension
gobuster vhost -u url -w wordfile -t 1000 -o outputfi -k --append-domain
gobuster dns -d google.com -w wordfile -i -t 1000 -o output
ffuf
ffuf -u http://slfas.com/FUZZ -w wordfile -t 1000
ffuf -u http://slfas.com/FUZZ -w wordfile -t 1000 -e html,php
ffuf -u http://FUZZ.slfas.com/ -w wordfile -t 1000
wpscan
wpscan --update
wpscan --url http://sdfa.cm --enumerate or -e t or p or u or at or pt or vt or vp
wpscan --url http://ssld.cm --usernames gana --passwords wordfile
/wp-content/themes/themename 
/wp-content/plugins/pluginname
nikto
nikto --list-plugins 
nikto -h url_ip_domainName -Plugin pluginName -Display 1_2_E -Tuning 1_2_..
Metasploit Framework
Location - /usr/share/metasploit-framework
Important directory modules -> contains modules used in MSF -> auxiliary -> for port scanning,fuzzing,sniffing -> encoders -> to encode the payload -> exploits -> to take advantage of vulnerability -> payloads -> code execute on remote sys -> nops -> used for cpu process allocation hacking
 -> Module Two Types
    -> Primary in default location
	-> Customs -> ~/.msf5/modules
Loading modules -> while starting msf cmd -> msfconsole -m ~/secret-module/ -> inside the msf framework -> msf>loadpath ~/secret-module/
Initializing MSF db - used to store results of attacktype
 -> service postgresql start
 -> msfdb init -> create database and table for our uses

	
Nmap Scanning Types:
TCP Connect Scan(-sT):
      Open: 3 way handshake is done. 
      Closed:  RST flag is received for SYS Flag.
      Filter: If No response is received
Its is slow, firewall can be configured to send RST flag for open ports too.

TCP SYS Scan (-sS):
It is called as Half Open scan or SYN Scan . It is more Stealtier than Tcp connect scan and faster.
     Open: After receiving the ACK for SYN , RST will be sent
     Closed: When we receive RST for SYN flag
     Filter: If we receive nothing.
UDP Scan (-sU):
 UDP is not stateless protocol, it will send the packets hopping it will reach the target.
       Open or Filtered: If we not get any response then it will be open or filtered. If we get response then it is Open other that ICMP echo request meaning the packet is unreachable, Then Port is Closed.
Other Scanning Techniques:
The following scanning techniques are more stealthier than the Syn Scan
Null Scan(-sN): TCP packet is sent with null flag set or no flag set. If we receive RST then closed.
FIN Scan (-sF): TCP packet is sent with null FIN bit set or no flag set. If we receive RST then closed.
Xmas Scan(-sX): TCP packet is sent with the FIN, PSH, and URG flags.If we receive RST then closed.
For all the above 3 types if we not receive any response then the port is open or filtered. Filtered means the port is open and filtered by the firewall. If we receive ICMP Ping back then the port is filtered
Privilege escalation tools
LinEnum
Linpeas

Steganography Tools & Tips
file - command to know about file type
strings - command to extract all strings from a file
xxd - 
hexdump -
hexeditor - to edit the data of a file in bytes
binwalk - tools to check whether a image binary has any hidden file
stegnohide  - tool to hide , extract data hidden in a file
stegnohide --embed -cf coverFile -ef hiddenfile
stegnohide --extract -sf coverFile 
stegnohide --info coverFile 
stegseek - used to bruteforce passphrase protected file
stegbrute -  used to bruteforce passphrase protected file 
7z - tool to unzip zip file & check its content 
