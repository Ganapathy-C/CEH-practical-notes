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

in this lab we are creating one in [[IIS]]
but we can also do this in [[Apache]]

to check if our website has ssl certificate
→ launch a browser
→ enter your website URL with "https" *here `https://www.goodshopping.com`*
→ it shouldn't load

→ search for IIS in the windows search and open it
→ click on the machine name in the left pane
→ double click the `Server Certificates`Under the IIS section
→ in the right pane `Create a self-signed Certificate`
→ enter the details, choose `Personal` option from the drop down menu
→ it'll create a new self signed certificate that is visible in the server certificates section
→ in the left pane expand the `Sites`  and click the website that needs to be on the HTTPS 
→ after choosing, select `Bindings`from the right pane
→ click `Add`
→ enter the details , choose port 443
→ select the SSL certificate from the drop down menu
→ right click the website name in the left pane and select `Refresh`
→ reload the page in browser and we should see a different warning this time
→ accept the risk and continue

---

## Perform Disk Encryption

tool used
#### [[VeraCrypt]]
#tool/windows #gui 

we can use it to find all the files into a secret volume
→ select the file
→ mount it onto a random drive
→ enter password
→ open file explorer and explore the drive



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