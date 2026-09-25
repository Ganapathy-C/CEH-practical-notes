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

→ in a browser window https://certifiedhacker003.s3.amazonaws.com
> https://<bucket-name\>.s3.amazonaws.com
> this will list the directories and files available in the bucket

→ moving a file to the bucket
```bash
echo "you are hacked" >> hack.txt
```

```bash
 aws s3 mv hack.txt s3://<s3-bucket-name>

 aws s3 mv hack.txt s3://certifiedhacker003
```
> now we reload the browser page and see that there's a file named `hack.txt`

→ removing the file from the bucket
```bash
 aws s3 rm s3://<s3-bucket-name>

 aws s3 rm s3://certifiedhacker003/hack.txt
```
> reload the page and we see that the file is deleted

## Perform Privilege Escalation

### escalate IAM user privilege via misconfigured user policy

→ refer Manual for detailed and understandable explanation

- In the **terminal** , type **sudo su** and press Enter to run the programs as a root user and user toor as password.
- After configuring the AWS CLI, we create a user policy and attach it to the target IAM user account to escalate the privileges
-  In the terminal window, type **vim user-policy.json** or **pluma user-policy.json** and press Enter.  A command line text editor appears; **press I** and type the script given below:
```text\

{
"Version":"2012-10-17",
"Statement": [
 "Effect":"Allow",
 "Action":"*",
 "Resource":"*"
}
]
}
```
- Note: This is an AdministratorAccess policy that gives administrator access to the target IAM user.
- Note: Ignore the $ symbols in the script.
- After entering the script given in the previous step, **press the Esc button**. Then, type **:wq!** and press Enter to save the text document.
- Now, we will attach the created policy (user-policy) to the target IAM user’s account.
- ```bash
   aws iam create-policy --policy-name user-policy --policy-document file://user-policy.json
  ```
- The created user policy is displayed, showing various details such as **PolicyName**, **PolicyId**, and **Arn**.
- We **need to create an user** in **AWS IAM** with **name test** and giving **access to AWS IAM** and **setting up the password**.
- **Copy** the **policy arn** from above and use it below 
```bash
 aws iam attach-user-policy --user-name [Target Username] --policy-arn arn:aws:iam::[Account ID]:policy/user-policy

 aws iam attach-user-policy --user-name test --policy-arn arn:aws:iam::2342897234:policy/user-policy
```
- The above command will attach the policy (user-policy) to the target IAM user account (here, test).
- To list attached user policys
```bash
aws iam list-attached-user-policies --user-name [Target Username]

aws iam list-attached-user-policies --user-name test
```
- Now that you have successfully escalated the privileges of the target IAM user account, you can list all the IAM users in the AWS environment
```bash
 aws iam list-users
```
- Similarly, you can use various commands to obtain complete information about the AWS environment such as the list of S3 buckets, user policies, role policies, and group policies, as well as to create a new user.
```bash
   ▪ List of S3 buckets: aws s3api list-buckets --query "Buckets[].Name"
   ▪ User Policies: aws iam list-user-policies
   ▪ Role Policies: aws iam list-role-policies
   ▪ Group policies: aws iam list-group-policies
   ▪ Create user: aws iam create-user
```
- This concludes the demonstration of escalating IAM user privileges by exploiting a misconfigured user policy.




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
