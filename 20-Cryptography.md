# Cryptography

## Encryption using various tools

### Multi Layer hashing
tool used
#### [[CyberChef]]

- https://gchq.github.io/CyberChef

we can perform multi layer hashing, decoding, encryption all that in that one website

**set breakpoints** to stop the baking at that step
**disable operation** for pausing a specific step


### perform File and text Message encryption

tool used
#### [[CryptoForge]]

⇒ file encryption

→ create a file with secret message / information / data
→ `right-click`and click `Encrypt`
→ enter a passphrase twice and click `OK`
→ now we transfer this file to someone we wish to give
→ they open the file by *double clicking* the file
→ they need the passphrase in order to access the file, which we can share through other means such as email, SMS, etc.

⇒ message encryption

→ on the desktop in the search menu, type `cryptoforge text`and open
→ we have a CryptoForge Text , we type a message and click `Encrypt`
→ enter a passphrase twice and `OK`
→ the message will get encrypted and then we click `Save as`

to decrypt 
→ double click the file
→ the file opens but it is in encrypted form
→ click `Decrypt`
→ enter the passphrase
→ *Voila*

---
## Creating an [[SSL]]

an SSL = Self Signed Certificate

in this lab we are creating Self Signed Certificate  in [[IIS]] Manager
but we can also do this in Server Manger via Active Directory Certificate Services(ADCS)

to check if our website has ssl certificate
- launch a browser
- enter your website URL with "https" *here `https://www.goodshopping.com`*
- it shouldn't load

- Turn on the Windows Server 2019 virtual machine. 
- search for IIS in the windows search and open it
- click on the machine name(**Server2019**) in the left pane
- In the **Middle pane** -> **Click on Server Certificates**
- double click the **Server Certificates** in the **righthand pane**
- The Create Self-Signed Certificate window appears; type **GoodShopping** in the Specify a **friendly name** for the certificate field. Ensure that the **Personal option** is **selected** in the Select a certificate store for the new certificate field; then, click OK.
- **Expand** the **Sites node** from the **left-hand pane** and select **GoodShopping** from the available sites. **Click Bindings**... from the **right-hand pane** in the **Actions** section.
- The **Add Site Binding** window appears; choose **https** from the Type field drop-down list. Once you choose the https type, the port number in the Port field automatically changes to **443** (the channel on which HTTPS runs).
- Choose the **IP address** on which the site is hosted (here, **10.10.1.19**).
- Under the **Host name** field, type **www.goodshopping.com**. Under the **SSL certificate** field, select **GoodShopping** from the drop-down list, and click OK
- Now,**right-click the name of the site** for which you have created the self-signed certificate (here, **GoodShopping**) and **click Refresh** from the context menu.
-  Open the **Mozilla Firefox** browser and go to **https://www.goodshopping.com**. 

---

## Perform Disk Encryption

tool used
#### [[VeraCrypt]]
#tool/windows #gui 

we can use it to hide all the files into a secret volume.
- **Open VeraCrypt**. The VeraCrypt main window appears. click the **Create Volume** button.
- The **VeraCrypt Volume Creation Wizard** window appears. Ensure that the **Create an encrypted file container radio-button** is selected and click **Next** to proceed.
- In the **Volume Type wizard**, keep the **default settings** and click **Next**.
- In the **Volume Location** wizard, click **Select File**....
- The **Specify Path and File Name window** appears; navigate to the desired location (here, Desktop), provide **the File name as MyVolume**, and click **Save**
- After **saving the file**, the **location** of a **file** containing the VeraCrypt volume appears under the **Volume Location field**; then, click **Next**.
- In the **Encryption Options wizard**, keep the **default** settings and click **Next**.
- In the **Volume Size wizard**, ensure that the **MB radio-button** is selected and specify the **size of the VeraCrypt container as 5**; then, click Next.
- The **Volume Password wizard** appears; provide a **strong password in the Password field**, retype in the Confirm field, and click **Next**. The **password** provided in this lab is **qwerty@123**. Note: A VeraCrypt Volume Creation Wizard warning pop-up appears; then, click **Yes**.
- The Volume Format wizard appears; ensure that **FAT is selected in the Filesystem option** and Default is selected in Cluster option.
- Check the **checkbox under the Random Pool, Header Key, and Master Key** section.
- Move **your mouse as randomly as possible within the Volume Creation Wizard window for at least 30 seconds** and click the **Format** button.
- After **clicking** Format, **VeraCrypt** will create a file called **MyVolume** in the provided folder. This file depends on the VeraCrypt container (it will contain the encrypted VeraCrypt volume)
- Depending on the size of the volume, volume creation may take some time.
- Once the **volume** is **created**, a VeraCrypt Volume Creation Wizard dialog-box appears; **click OK**.
- In the **VeraCrypt Volume Creation Wizard** window, a **Volume Created message appears**; then, click **Exit**.
- The **VeraCrypt main window appears**; s**elect a drive (here, I:**) and click **Select File**....
-  The Select a VeraCrypt Volume window appears; navigate to Desktop, click **MyVolume**, and click **Open**.
- The window closes, and the **VeraCrypt window appears displaying the location of selected volume under the Volume** field; then, click **Mount**.
- The Enter password dialog-box appears; type the **password** you specified in Step#11 into the **Password** **field** and click OK.
-  After the password is verified, VeraCrypt will **mount** the **volume in I: drive**, as shown in the screenshot.
- **MyVolume** has **successfully** **mounted** the container as a virtual disk (I:). The virtual disk is entirely encrypted (including file names, allocation tables, free space, etc.) and behaves similarly to a real disk. You can copy or move files to this virtual disk to encrypt them.
- **Create a text file on Desktop and name it Test**. Open the text file and insert text.
- Click File in the menu bar and click Save
- **Copy the file from Desktop and paste it into Local Disk (I:)**. Close the window.
- Switch to the **VeraCrypt window**, click **Dismount**, and then click **Exit**.
- The I: drive located in This PC disappears.
  

### Steganography

tool used 
#### [[Steghide]]
#tool/linux 

perform steganography using [[steghide]]
```
steghide embed -cf <cover-file(.jpeg|.jpg)> -ef <secret-file(.txt)> -p 1234
```
> -p = passphrase
and to extract 
```
steghide extract -sf <secret-file> -xf <extracted-file> -p "<passphrase>"
```

but steghide works best when the file is an image or a audio file


#### [[Snow]]
#tool/linux 

snow is also a steganography tool
- works best with a text file

```bash
snow -C secret.txt
```
> to hide the data

```bash
snow -S secret.txt
```
> to extract the data

#### Open Stego
#tool/windows #gui 

it is a GUI tool that does the same thing as above
