
| protocol name                                    | TCP port   | UDP port |
| ------------------------------------------------ | ---------- | -------- |
|                                                  | 2002       |          |
| ADB (android debug bridge)                       | 5555       |          |
| Default port of portable python server           | 8000       |          |
| DNS                                              | 53         | 53       |
| FTP                                              | 21         |          |
| HTTP                                             | 80         |          |
| HTTPS                                            | 443        |          |
| Kerberos-sec                                     | 88         |          |
| LDAP                                             | 389        |          |
| LDAPS (LDAP over SSL/TLS)                        | 636        |          |
| Microsoft ADGC (active directory global catalog) | 3268, 3269 |          |
| Modbus (industrial communications)               | 502        |          |
| MQTT (IOT and OT)                                | 1833       |          |
| MSSQL                                            | 1433       |          |
| MySQl                                            | 3306       |          |
| NFS                                              | 2049       |          |
| njrat default port                               | 5553       |          |
| Proxy / WAMP server                              | 8080       |          |
| RAT Ports                                        | 9871, 6703 |          |
| RDP                                              | 3389       |          |
| RPC                                              | 135        |          |
| SMB                                              | 445        |          |
| SMB (NetBIOS)                                    | 139        |          |
| SNMP                                             |            | 161, 162 |
| SSH                                              | 22         |          |
| Telnet                                           | 23         |          |
| SMTP                                             | 25         |          |
| POP3                                             | 110        |          |
| POP3 (encrypted)                                 | 995        |          |
some common remote login protocols

- **FTP** → `ftp <ip>`
- **SSH** → `ssh user@<ip>`
- **Telnet** → `telnet <ip> <port>`
- **SMB (smbclient)** → `smbclient //<ip>/<share>`
- **RDP** → `xfreerdp /v:<ip>`
- **MySQL** → `mysql -h <ip> -u user -p`
- **ADB** → `adb connect <ip>:5555`