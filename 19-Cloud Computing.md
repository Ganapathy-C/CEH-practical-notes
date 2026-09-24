# Cloud Computing

### Reconnaissance on Azure

tool used
#### [[AADInternals]]
#tool/windows 

-> On Windows machine, Navigate to E:\CEH-Tools\CEHv13 Module 19 Cloud Computing\GitHub Tools\ and copy **AADInternals** folder and paste it on Desktop.

-> In the Windows search type powershell and under PowerShell click on Run as Administrator to open an administrator PowerShell window.

→ in the `PowerShell`
```PowerShell
cd C:\Users\Admin\Desktop\AADInternals
```
```PowerShell
 Install-Module AADInternals
```
-> Note: In the Do you want PowerShellGet to install and import the NuGet provider now? Question **type Y** and press Enter. In the Are you sure you want to install the modules from **“PSGallery**”? question **type A** and press Enter

```PowerShell
 Import-Module AADInternals
```
```PowerShell
 Invoke-AADIntReconAsOutsider -DomainName company.com | Format-table
```
> replace <company name\> with the target company *here eccouncil.org*
> from this we get info like -
> - DNS
> - MX
> - SPF
> - DMARC
> - DKIM etc.

→ now we perform **User Enumeration** in Azure AD

```PowerShell
Invoke-AADIntUserEnumerationAsOutsider -UserName user@company.com
```
> replace \<user\@company.com> with target user and company name i.e. email address *here company name is eccouncil.com*
> if the user exists we get **True** and **False** if not

→ if we want to enumerate multiple usernames at once, we can save the names of the email addresses in a file `users.txt`
```PowerShell
Get-Content .\users.txt | Invoke-AADIntUserEnumerationAsOutsider -Method Normal
```

→ To get the tenant ID for the given user, domain, or Access Token
```PowerShell
Get-AADIntTenantID -Domain company.com
```

→ to get login information for a user
```PowerShell
Get-AADIntLoginInformation -Domain user@company
```
> replace \<user\@company.com> with target email address

→ to get the tenant ID for the given user, domain, or Access Token
```PowerShell
Get-AADIntTenantID -Domain company.com
```

→ to get registered domains from the tenant of the given domain
```PowerShell
Get-AADIntTenantDomains -Domain company.com
```

- https://aadinternals.com/osint/
#website 

→ type the **tenant id**, **domain name**, or **email** to get the openly available information for the given tenant.
→ type the **domain name** in the **search box** and click on **Get information** button.

---

## Exploit S3 Buckets

### Exploit open S3 Buckets

tool used
#### AWS CLI

first we need to create an AWS root user account, go to  Click the AWS account drop-down menu  -> security credentials -> access keys. create access keys

- AWS Access Key ID
- AWS Secret Access Key
- Default region name
- Default output format


→ in the root terminal
```bash
sudo su
cd
```
> to move to the root home directory

→ to install AWS CLI
```bash
 pip3 install awscli
```

-> To configure AWS cli
```bash
aws configure
```

→ https://console.aws.amazon.com
→ get the above listed keys
→ set default region name = eu-west-1
→ leave default output format as is

→ now to enumerate s3 buckets
```bash
aws s3 ls s3://<s3-bucket-name>
```
> this will show you the list of directories in the specified bucket

→ in a browser window https://certifiedhacker02.s3.amazonaws.com
> https://<bucket-name\>.s3.amazonaws.com
> this will list the directories and files available in the bucket

→ moving a file to the bucket
```bash
echo "you are hacked" >> hack.txt
```

```bash
aws s3 mv hack.txt s3://<s3-bucket-name>

 aws s3 mv hack.txt s3://certifiedhacker02
```
> now we reload the browser page and see that there's a file named `hack.txt`

→ removing the file from the bucket
```bash
 aws s3 rm s3://<s3-bucket-name>

 aws s3 rm s3://certifiedhacker02/hack.txt
```
> reload the page and we see that the file is deleted

## Perform Privilege Escalation

### escalate IAM user privilege via misconfigured user policy

→ refer Manual for detailed and understandable explanation

## Vulnerability assessment

### assessment on docker images

tool used
#### [[Trivy]]

in this we perform assessment on a secure and a vulnerable docker image to see the difference

a secure docker image
```bash
docker pull ubuntu
```

→ analyze
```bash
trivy image ubuntu
```
> we see that we have 0 vulnerability 

now a vulnerable image
```bash
docker pull nginx:1.19.6
```

→ analyze
```bash
trivy image nginx:1.19.6
```
> we see that we have more than 400 vulnerabilities and their severity

---
