
---

# [AWS IAM Security Lab](https://www.youtube.com/watch?v=QdVmLA2y3MA)

---

**Credits**

- This project was inspired by:
	- [NextWork](https://www.youtube.com/@itsnextwork)

---

In AWS, a user is a person or a computer that can do things on the cloud - just like you right now! Today, we'll be using the **AWS Identity and Access Management (IAM)** tool to control who is authenticated (signed in) and authorized (has permissions) in your AWS console. We'll launch an EC2 instance, then control who has access to it by creating some IAM policies and user groups. It will look something like this:

![image](https://github.com/user-attachments/assets/8780e349-142c-4990-900e-9606730e6f32)

**What you'll learn today:**
✔️ Create and manage IAM users and groups
✔️ Implement the principle of least privilege
✔️ Configure IAM policies for precise access control
✔️ Set up multi-factor authentication (MFA)

**🤷‍♀️ WHY DO THIS PROJECT?**
Security is non-negotiable in cloud computing. This project will:
- Make you a more responsible AWS user
- Teach you how to protect valuable cloud resources
- Demonstrate security skills that employers value highly
- Complete your foundation of essential AWS knowledge

**Get ready to create (and learn from scratch):**
1. 💻 EC2 instances
2. 📏 IAM Policies
3. 👩‍👩‍👧‍👧 IAM Users and User Groups
4. 🔖 AWS Account Alias

- **Key Skills/Tools:** AWS IAM, EC2, IAM policy design, MFA setup

**Let's Begin!**

---


## 🖥️ Step 1: Launch EC2 Instances with tags

- In this project, we are joining a new company and preparing for the upcoming summer break. Our tasks include **boosting computing power to handle increased website traffic** and **onboarding a new intern, ensuring they have the ==appropriate permission settings== to contribute effectively while keeping the company’s resources secure.**

- Let's start with the first task and **boost computing power** by launching some EC2 instances.

- [Log in to your AWS Management Console](https://console.aws.amazon.com/).
- ![image](https://github.com/user-attachments/assets/954bc0f5-59d7-43a9-b8fa-4993082ec5e7)
- Open your `EC2` console - search for it at the search bar.
- ![image](https://github.com/user-attachments/assets/76fb1c76-723a-4ea9-a777-aed2d126c116)
- After clicking on `EC2` you will be brought here:
- ![image](https://github.com/user-attachments/assets/1b8f2943-2c0a-4e53-bd16-421e5eb92d2d)

> 💡 **What is EC2?**  
> Amazon EC2 is a service that lets you rent and use virtual computers in the cloud. They're like your personal computers, but they exist on the internet instead of being physically in front of you. You can create, customize, and use these computers for all different reasons, from running applications to hosting websites.
> EC2 = **Elastic Compute Cloud.**
> 	Here's what the three words mean:  
> 		1️⃣ **Elastic** = flexible. This service can easily adapt and change in size and power to fit your needs.  
> 		2️⃣ **Compute** = computing power. EC2 provides virtual computers that can do various tasks, just like your personal computer.  
> 		3️⃣ **Cloud** = available over the internet.

- Switch your **Region** to the one closest to you!
- ![image](https://github.com/user-attachments/assets/1ddbcb86-9628-4214-866e-bcda831e1b9f)
- In your EC2 console, choose **Launch instance**.
- ![image](https://github.com/user-attachments/assets/bec936ea-786e-42b5-b02b-a23cebf466a3)

> 💡 **What are EC2 instances?**  
> - If EC2 is the service that provides virtual computers or servers, each instance is one of those computers or servers that gets produced.
> 
> - Just like you can choose a computer with more memory or a faster processor when you buy a laptop, with EC2 instances, you can pick a virtual computer that fits what you need for your projects. You can customize your EC2 instance's CPU, memory, storage, networking capacity and more!
> 
> 💡 **What's the difference between a virtual computer and a server?**  
> - A virtual computer is basically the same as a normal computer... only, without the screen, keyboard, and other things that make it super easy to use. Without these physical elements, it becomes virtual! When a computer is virtual, you can access it from your own physical computer - basically giving your normal computer super powers.  
>   
> - Turns out, a server is also a computer. But a server is specifically designed to be used by a whole group of other computers to help with things like more power or storage. Servers are great for things like hosting websites, storing files, or running games that multiple people play together, whereas virtual computers are designed to be an extension of a personal computer.

- After clicking **Launch instance** you will be brought to a screen similar to the image below. Here is where we need to set up our EC2 instance!
- ![image](https://github.com/user-attachments/assets/5741b26a-7768-4373-ae43-fdaac02444fb)
- We'll begin by creating a name. I'll create one in this format `companyname-prod-initials`, so `realcompany-prod-am`.
- ![image](https://github.com/user-attachments/assets/0a3ed785-2873-4afe-b247-0a8ba07ea56d)
- We need to add a tag, so click on **Add additional tags**, which is right next to your **Name** field.
	- Choose **Add new tag**.
	- For the next tag, use this information:
		- **Key**: `Env`
		- **Value**: `production`
- ![image](https://github.com/user-attachments/assets/dc35012c-bdde-4496-82ac-14eaf0257204)

> 💡 **Why are we creating a new tag? What does this tag mean, how will it be useful later?**  
>   
> - Tags are like labels you can attach to AWS resources for organization.
> 
> - In this case, we're creating a tag called "`Env`" with a value of "`production`" or "`development`" to label the instances used in production vs development environments.
> 
> - This tagging helps us with identifying all resources with the same tag at once (they are useful filters when you're searching for something), cost allocation, and applying policies based on environment types. You'll see the last point about policies in action soon!

- Scroll down to **Application and OS Images (Amazon Machine Image)** to see your EC2 settings and make sure the **Amazon Machine Image (AMI)** is using a **Free tier eligible** option. In our case, it says our AMI is **Free tier eligible**, meaning we may continue.
- ![image](https://github.com/user-attachments/assets/c394df55-98a6-4b24-a392-eb3a32847f95)

> 💡 **What is AMI? What is Free tier eligible?**  
> - When you buy a new computer off the shelf, most computers already have some software and the operating system (e.g. MacOS, Windows) already configured and set up for you!
> 
> - AMI stands for Amazon Machine Image, and it's very similar to those pre-built computers. An AMI is a template or blueprint used to create EC2 instances and contains the operating system along with the applications needed to launch the instance.
> 
> - **Free tier eligible** AMIs are those that qualify for the AWS Free Tier, so you won't get charged for using it.

- Scroll down to **Instance type**. For the instance type, also make sure you're using a **Free tier eligible** option! Looks like we are good to go here as well.
- ![image](https://github.com/user-attachments/assets/b4378d86-8ef1-41b5-a784-404aa4ef165f)

> 💡 **What is instance type?**  
> - If AMIs give you pre-built software and operating systems, then instance types cover all the "**hardware**" components.
> 
> - CPU power, memory size, storage space and more!
> 
> - So, while the AMI decides what operating system your server runs, the instance type determines how fast and powerful it performs.

- For **Key pair (login)**, select **Proceed without a key pair (Not recommended)**.
- ![image](https://github.com/user-attachments/assets/d18a533c-2e6d-4b27-8ee5-db983191829c)

> 💡 **What is a key pair? Why does it say (Not recommended) next to proceeding without one?**  
>   
> - A key pair is primarily used for accessing your EC2 instance securely **without going through the AWS Management Console**. Instead of the Management Console, you're using SSH (Secure Shell) Access with your key pairs - this is out of scope for this project.
> 
> - Proceeding without a key pair means you won't have SSH (Secure Shell) access to your instance, which is generally not recommended because it limits your ability to troubleshoot or manage your EC2 instance through a secure way outside of the Console. It's always safer and more flexible to have a key pair set up, so you would create a key pair for bigger projects that you work on over a longer period of time.

- Skip the **Network settings, Configure storage, & Advanced details** sections.
- ![image](https://github.com/user-attachments/assets/06fc0378-0eb6-4851-ab75-64452494df6f)
- You're ready! Click **Launch instance**.
- ![image](https://github.com/user-attachments/assets/9480511e-0cac-4f85-bbe2-ac3cfe1821b6)

> 💡 **What are the settings we've skipped just now?**  
> - We skipped configuring network and storage settings for simplicity in this project. These settings are crucial for fine-tuning your EC2 instances' performance, security, and connectivity, but for this project, we'll focus on the basic steps of launching instances with minimal configuration.
> 
> - **Network settings** define how your instances interact with the internet and other AWS resources, determining factors like IP addresses and network routing.
> 
> - **Storage settings** involve choosing the type and size of storage volumes (like hard drives) that your EC2 instance will use to store data.

- Looks like a success! Now let's create one more EC2 instance for the **development environment**.
- ![image](https://github.com/user-attachments/assets/591b1f8c-8c4e-47ef-a3ae-e8a4dcb09a1f)

> 💡 **What do the development vs production environments mean?**  
> - Development and production environments refer to different stages in the software development lifecycle.
> 
> - The **development** environment is where developers write, test, and debug code before it's deployed to **production**, which is the live environment that your end users can use!

- We'll repeat the same flow, but this time using these tags:
	- **Name**: `realcompany-dev-initials`
	- **Env**: `development`
- To launch your second instance, select **Instances** from your left hand navigation panel.
- ![image](https://github.com/user-attachments/assets/70ae4896-ab18-4b3f-b961-a248a089b656)
- You'll be brought to the **Instances** page. Click **Launch Instances** in the top right corner.
- ![image](https://github.com/user-attachments/assets/5fe2dfe7-6a63-4ccd-b1c6-1de124c25964)
- You're brought back to the creation screen. let's now repeat the same steps but with for our `development` environment instead. So for me the name would be `realcompany-dev-am` and the additional tag would be Key: `Env` & Value: `development`.
- ![image](https://github.com/user-attachments/assets/2e470405-be88-4c90-86df-13cf76c1bee8)
- Scroll down to **Key pair (login)** and select **Proceed without a key pair (Not recommended)**.
- ![image](https://github.com/user-attachments/assets/61a9986a-3a19-4fdb-86e3-4f00d0d5b179)
- Keep everything else as default and click **Launch Instance**.
- ![image](https://github.com/user-attachments/assets/61fbe3c6-6392-4d82-88da-5bdfad91aef9)
- Another successful launch!
- ![image](https://github.com/user-attachments/assets/9d708709-7508-4c8b-91f6-1df6997b539d)
- Click back on the **Instances** page to confirm they are there. While on this page, if you select the checkbox next to one of your instances, and a popup window of information pops up!
- ![image](https://github.com/user-attachments/assets/3d03e466-69e1-40b6-b99c-0a01bc85f14a)
- Select the **Tags** tab and you'll see the tags you've defined right here.
- ![image](https://github.com/user-attachments/assets/e80068bf-3603-4025-95dd-cfe681cabc04)
- That completes **Step 1 - Launch EC2 Instances with tags**! You've deployed two EC2 instances, one for your **`production`** environment and one for your **`development`** environment.

> - Now let's move into our second task as our company's engineer. It's time to onboard the team's new intern and **set up permission policies.**
> 
> - Our intern should have permission to the development EC2 instance but **not** the production instance. We don't want them to accidentally shut down the platform or push their changes to the production environment while they're just testing things!
> 
> - To start this task, we'll use AWS IAM to **give** our intern access to the development instance first.

---

## 📏 Step 2: Create an IAM Policy

- **In this step, get ready to:**
	- Create an IAM policy that gives access to the development instance.

- Head to your `IAM` console using the search bar.
- ![image](https://github.com/user-attachments/assets/f8380824-4a5f-424b-bf36-0f100c54a35d)

>💡 **What is IAM?**  
> - IAM stands for Identity and Access Management. You'll use AWS IAM to manage the access level that other users and services have to your resources.

- You will now be brought to the **IAM Dashboard**. Take a look on the left-hand navigation panel of your IAM console, then click **Policies**.
- ![image](https://github.com/user-attachments/assets/955c6119-22c5-4cf1-afc6-ee946d64501a)

>💡 **What is a Policy?**  
> - An IAM policy is a rule for who can do what with your AWS resources. It's all about giving permissions to IAM users, groups, or roles, saying what they can or can't do on certain resources, and when those rules kick in.

- You'll now be on the **Policies** page. Choose **Create policy** in the top right hand corner.
- ![image](https://github.com/user-attachments/assets/9ffa937d-74f8-48aa-9218-d4dd80962326)
- Now on the **Create Policy** page, within the **Specify permissions** section, switch your Policy editor tab from "Visual" to "JSON".
- ![image](https://github.com/user-attachments/assets/65bca69a-174d-454e-a41d-10a201d4b51c)
- You should now see this:
- ![image](https://github.com/user-attachments/assets/2d2304d3-d5bf-4b3c-9227-d3bf270476ba)

>💡 **What are we doing right now?**  
> - You can create and edit AWS policies in the visual editor or JSON. In this project, we will use the JSON method.

Here's the policy you'll be using! Paste this policy into your editor - replace ALL of the existing code in your editor.

```json
{    
  "Version": "2012-10-17",    
  "Statement": [        
    {            
      "Effect": "Allow",            
      "Action": "ec2:*",            
      "Resource": "*",            
      "Condition": {                
        "StringEquals": {                    
          "ec2:ResourceTag/Env": "development"                
        }            
      }        
    },        
    {            
      "Effect": "Allow",            
      "Action": "ec2:Describe*",            
      "Resource": "*"        
    },        
    {            
      "Effect": "Deny",            
      "Action": [                
        "ec2:DeleteTags",                
        "ec2:CreateTags"            
      ],            
      "Resource": "*"        
    }    
  ] 
}

```

- ![image](https://github.com/user-attachments/assets/80c3a36c-d389-4fdf-a7eb-c8a3b3be9312)

> 💡**Let's explain this policy**  
> - This policy allows some actions (like starting, stopping, and describing EC2 instances) for instances tagged with "Env = development" while denying the ability to create or delete tags for all instances.
>   
> **💡 Extra for Experts: how are JSON policies are structured?**  
> - _Version_
> 	- This means 2012-10-17 is the date of the latest policy version. This tells you whether the policy is up to date with the latest standards and practices.
> 
> - _‍Statement_
> 	‍- The main part of the policy structure and defines a list of permissions.
> 
> - _‍Effect_
>	-  ‍This can have two values - either **Allow** or **Deny** - to indicate whether the policy allows or denies a certain action. **Deny** has priority. Looking at the first statement, "Effect": "Allow" means this statement is trying to allow for an action.
> 
> - _‍Action_
>	-  ‍A list of the actions that the policy allows or denies. In this case, "Action": "`ec2:*`" means all actions that you could possibly take on EC2 instances are allowed.
> 
> - _Resource_
>	-  ‍Which resources does this policy apply to? Specifying "`*`" means all resources within the defined scope (see the next point).
> 
> - _Condition Block (optional)_
>	-  ‍The circumstances under which the policy is in action. In this case, the condition is that the resource is tagged **Env - development**. This means specifying "Resource": "`*`" in the line above means all resources with the **Env - development** tag are impacted by your statement.

- Select **Next** when you're ready.
- ![image](https://github.com/user-attachments/assets/4b5a15d0-8114-4f4d-a368-7e20b0aafc4a)
- You should now be on the **Review and create** page.
- ![image](https://github.com/user-attachments/assets/7c53f685-c8c9-4c54-8c52-06f4017ab499)
- Fill in your policy's details:
    - **Name**: `RealCompanyDevEnvironmentPolicy`
    - **Description**: `IAM Policy for the RealCompany development environment.`
- ![image](https://github.com/user-attachments/assets/3e520317-dc1e-4653-8f41-84994fedc852)
- Take a look at the summary of what this policy does in the **Permissions defined in this policy** section. If everything looks OK, then you may continue. We are skipping the **Add tags - _optional_** section. Once everything looks good, click **Create policy**.
- ![image](https://github.com/user-attachments/assets/7c3a7316-3cd2-4d92-a09b-847a3d076439)
- Success!
- ![image](https://github.com/user-attachments/assets/cdbace85-cacc-4d97-86c9-2a44b85a3e24)
- That completes **Step 2 - Create an IAM Policy**!

> - Now that the permission policy is all set up, we can give our intern access to the development instance. The intern can't wait to start! They'd love to jump into the team's AWS account right away!
> 
> - Now that sounds great... where should they go to log in?
> 
> - Let's find out how to do that in Step 3!

---

## 🔖 Step 3: Create an AWS Account Alias

- **In this step, get ready to:**
	- Make it easier for users to log into your AWS account, using an Account Alias.

- Head to your **IAM Dashboard**. In the right-hand side of the dashboard, click **Create** under **Account Alias**.
- ![image](https://github.com/user-attachments/assets/ce1f883d-a414-477a-b65b-53cc4437cf2e)

> 💡 **What is an Account Alias? Why are we creating one?**  
> - Once you onboard new users into your AWS account (which we'll do for our new company intern), these new users get access through a unique log-in URL for your account.
> 
> - An Account Alias is a friendly name for your AWS account that you can use instead of your account ID (which is usually a bunch of digits) to sign in to the AWS Management Console.
> 
> - Your AWS account's sign-in page has this URL by default: https://**Your_Account_ID**.signin.aws.amazon.com/console/
> 
> - If you create an AWS account alias for your AWS account ID, your sign-in page URL looks more like: https://**Your_Account_Alias**.signin.aws.amazon.com/console/
> 
> - You would create an alias to make it easier to remember and share your AWS console's login URL with others e.g. your company's new intern. Companies often use this so that their AWS account sign-in page is more user-friendly for their users!

- After clicking **Create** you will have a popup to create your alias.
- ![image](https://github.com/user-attachments/assets/a489a8bd-642d-4c0d-b2e5-f25b95da6ffd)
- In the **Preferred alias** field, enter:
	- `company-alias-enter your name`
	- Replace **enter your name** with your name, and change the company as you see fit! In that case, mine is `realcompany-alias-am`
- When done, click **Create alias**.
- ![image](https://github.com/user-attachments/assets/176bfcca-e223-4afb-af95-1b0b46a91367)
- Success! Now we can see out new alias on the right-hand side and a green confirmation banner at the top.
- ![image](https://github.com/user-attachments/assets/36b8c6dc-0a6c-46d7-b6ab-7d87bcc37f00)
- That completes **Step 3 - Create an AWS Account Alias**!

> - Your account alias is looking great! It should be a lot faster for users to find your account and log in now.
> 
> - Our new intern doesn't actually have a way to log in to the team's AWS account yet... what should their username and password be?
> 
> - You wouldn't want to just share your account details with them. After all, you have access to the production instance, which they shouldn't get access to.
> 
> - Let's solve this problem using IAM again - this time we're using two other tools called groups and users.

---

## 👨‍👩‍👧‍👦 Step 4: Create IAM Users and User Groups

- **In this step, get ready to:**
	- Set up a dedicated IAM **group** for all the company's interns, so you can manage all interns' permissions from one place.
	- Set up a dedicated IAM **user** for your new intern, so they have a way to log in.

- While still within **IAM**, click **User groups** in your left-hand navigation panel.
- ![image](https://github.com/user-attachments/assets/00f43fd4-d211-4a06-970d-f2d0e7b4bfd6)
- Choose **Create group** to create our first user group!
- ![image](https://github.com/user-attachments/assets/94db9a31-1e3f-41c0-af90-43eb192be67e)

> 💡 **What is an IAM user group?**  
> - An IAM user group is a collection/folder of IAM users. It allows you to manage permissions for all the users in your group at the same time by attaching policies to the group rather than individual users.

- Here is the **Create user group** page
- ![image](https://github.com/user-attachments/assets/b4d3896d-f896-4804-98fd-402e698e175d)
- To set up your user group, choose:
    - Under the **Name the group** section:
        - **User group name**: `realcompany-dev-group`
    - Skip **Add users to the group - _Optional_** section.
    - Under the **Attach permissions policies - _Optional_** section:
        - Attach permission policies: `RealCompanyDevEnvironmentPolicy`
- ![image](https://github.com/user-attachments/assets/8182284d-e18f-41c1-9e4b-8b3504b9b0d6)
- Select **Create user group**. Success!
- ![image](https://github.com/user-attachments/assets/4976976a-ddc7-47ce-9a61-6ee27c2f3411)

> 💡 **Why do we need users in our user group?**  
> - IAM users are the people that will get access to your resources/AWS account, whereas user groups are the collections/folders of users for easier user management.
> 
> - We're adding users to **`realcompany-dev-group`** to grant them the permissions associated with that group.
> 
> - This simplifies managing permissions and ensures consistency across users who have similar access to AWS resources. Imagine if you have a whole team of 5 interns (users) that need the same permission settings!

- Now let's add Users to your user group. Click **Users** from the left-hand navigation panel.
- ![image](https://github.com/user-attachments/assets/07c8a23d-ad2a-47de-ab6d-c3fd0218a58a)
- Click **Create user**.
- ![image](https://github.com/user-attachments/assets/7547cef8-3803-42ab-9b09-a0ce6ceb4095)
- We are now at the **Create user** page:
- ![image](https://github.com/user-attachments/assets/a6ad0b38-2b38-46e9-9a5d-a6db83b4ee38)
- Let's set up this user! Under **User name** in the **User details** section, I'll enter:
	- `realcompany-dev-intern`
	- Tick the checkbox for **Provide user access to the AWS Management Console**.
	- Select "**I want to create an IAM user**"
- ![image](https://github.com/user-attachments/assets/e63766d0-ecfa-4273-b086-26371272cc71)

>💡 **Why are we ticking this box?**  
> - If you don't tick this box, your new user won't get to sign in and access AWS services through the Console. They'll have to access AWS services through other, more advanced methods.

- Under the **Console password** subsection:
	- Leave **Autogenerated password** selected.
	- Uncheck the box that says **Users must create a new password at next sign-in - Recommended**.
- ![image](https://github.com/user-attachments/assets/5510978a-daed-431a-87bb-60dd463b3e7d)

>💡 **WARNING**: In the real world, you should absolutely leave this box checked! We are leaving it unchecked because you'll have to create a new password for this user, which is irrelevant to our learning objectives for today.

- Select **Next** when you're ready!
- ![image](https://github.com/user-attachments/assets/12137538-ce14-47d3-8be8-59f044771e2f)
- We're now on the **Set permissions** page. To set permissions for your user, we'll simply add it to the user group you've created. Select the checkbox next to **`realcompany-dev-group`**. Select **Next**.
- ![image](https://github.com/user-attachments/assets/bc2a9271-456f-44be-9f87-686cb1a7018b)
- Now on the **Review and create** page, review your selections. When done, click **Create user**!
- ![image](https://github.com/user-attachments/assets/73243275-6b70-42d5-ab5c-87d17d693e84)
- Another success! Now you're seeing some specific sign-in details for your new user. Stay on this page.
- ![image](https://github.com/user-attachments/assets/8eee951a-d18f-4e7c-8d80-8bb5ab7778da)
- Our intern now has an account with a username and password which completes **Step 4 - Create IAM Users and User Groups**!

>  - The new intern is going to be happy to receive their keys to the company AWS account - well done 😎
>  
>  - Before we pass them their login details, let's test the interns' IAM User's access first. That way we can make sure that they have the right access to our development instance (and not the production instance).

---

## 🔒 Step 5: Test your intern's access

- **In this step, get ready to:**
	- Log into AWS using the intern's IAM user.
	- Test the intern's access to your production and development instance.

- Copy the **Console sign-in URL**. Do not close this tab!
- ![image](https://github.com/user-attachments/assets/224e1b3e-ff93-44cc-aa64-8505c8274c23)
- Open a new **incognito window** on your browser and paste the URL. You'll be brought here:
- ![image](https://github.com/user-attachments/assets/563faf94-9534-4ddf-99c2-15477c8f219e)
- Using the **User name** and **Console password** given in your IAM tab, let's log in!
- ![image](https://github.com/user-attachments/assets/73e9cb0b-ec37-433e-9e49-a6659ca9999d)
- ![image](https://github.com/user-attachments/assets/18e7820f-66e9-4c1e-83c3-f9c690f08375)
- Welcome back to your AWS console, but this time as the dev user that you've created for yourself.
- ![image](https://github.com/user-attachments/assets/dfccae8c-d4e7-4afd-8492-61443878268b)

>💡 As a new user, the AWS console will treat you as someone that is starting from 0 again. Awesome for the new team member that you'll be giving this User to!

- As a new user, you'll notice that some of your dashboard panels are showing **Access denied** already.
- ![image](https://github.com/user-attachments/assets/1f6baf23-83b5-4418-9bcb-9efc0cc824b1)
- Head to your **EC2** console, and make sure you're in the same **Region** as the one where you deployed your two production and development instances.
- ![image](https://github.com/user-attachments/assets/73d9d88e-f598-4503-8c01-349f23476772)
- Head to **Instances**.
- ![image](https://github.com/user-attachments/assets/d4308785-f75f-420c-aa77-81a670f65e31)
- Select your **production** instance, and in the **Actions** dropdown, select **Manage instance state**.
- ![image](https://github.com/user-attachments/assets/e1aa6ab0-7a67-4c89-ad07-5f9ee2eea313)
- Let's try to stop this instance. Select the **Stop option**, then **Change state**.
- ![image](https://github.com/user-attachments/assets/597f770d-1611-4cfe-aa53-2c4d960cfd52)
- Click **Stop**.
- ![image](https://github.com/user-attachments/assets/27ae94c1-2ffd-4c5b-93d6-e3a5d9d81596)
- We have been denied!
- ![image](https://github.com/user-attachments/assets/68ae4ae3-0d0c-47a9-86a3-0ec4e164b76c)

>💡 Yikes! At the top of your page, an angry-looking banner tells us we've failed to stop this instance. The banner tells us it's because we're not authorized! We don't have permission to stop any instance with the production tag.

- Now let's try to stop the development instance. Head back to the **Instances** page, and select the checkbox next to **`realcompany-dev-am`**. Under the **Actions drop-down**, select **Manage instance state**.
- ![image](https://github.com/user-attachments/assets/71ff0b10-5561-4644-af5d-518177b24df9)
- Select **Stop**, then **Change state**. Select **Stop**.
- ![image](https://github.com/user-attachments/assets/5bb0d7d7-63a4-4041-ad5b-61cad12ecf15)
- Success! This time we did have permission to stop our development instance.
- ![image](https://github.com/user-attachments/assets/d9c73d48-4eef-44c9-9f63-e55cc9780a33)
- **Step 5 - Test you intern's access** is complete! The intern can only access and interact with development resources and not production.

> - In the last step, you ended up **shutting down** the development instance to test your intern's access. Shutting down EC2 instances could get pretty disruptive for your other engineers and your users, so it's best practice to run these tests in another way.
> 
> - You can use a special IAM tool called the **Policy Simulator** to validate policies without affecting your actual AWS resources. Let's try it out!

---

## 💎 Secret Mission - IAM Policy Simulator

Welcome to this project's 🤫 exclusive 🤫 secret mission!

Your mission, should you choose to accept it, is to use the **IAM Policy Simulator** to test your users' permissions in a faster, more efficient way.

💎 **In this secret mission, get ready to:**
- Use the IAM Policy Simulator to test user access.
- Showcase your secret mission in your **project documentation.**

- Head back to your **main** AWS account (not the dev user). Then to you **IAM Dashboard**.
- ![image](https://github.com/user-attachments/assets/3361643b-412a-41ef-a991-5eb8bf6a17f5)
- Scroll down to see **Policy Simulator** on the right-hand side, under the **Tools** section. Click on it to open a new tab.
- ![image](https://github.com/user-attachments/assets/f1aff60c-763b-43f6-a7cc-431f9e7bfef8)
- You'll see the **IAM Policy Simulator** page.
- ![image](https://github.com/user-attachments/assets/cd88fdc7-1e17-423e-bea7-f1382ff9a28f)
- Look at the **Users, Groups, and Roles** section on the left side. Switch from **Users** to **Groups**.
- ![image](https://github.com/user-attachments/assets/2f82b6a0-6105-4bbc-983b-cc1961b918a0)
- Click on your dev user group directly.
- ![image](https://github.com/user-attachments/assets/f4f9a1ca-e37f-4903-8d60-c861cdbb288a)
- Now look at the right side for the **Policy Simulator** where we will need to select a service to test.
- ![image](https://github.com/user-attachments/assets/26412bf0-63a5-4081-9f93-37bfb46a0835)
- Click the **Select service** drop down menu. Type in `EC2` and then select it in the menu.
- ![image](https://github.com/user-attachments/assets/04b0c183-7763-49e8-8951-98f2c3ad0dcd)
- Now we need to choose what actions we want to test with EC2. Click the **Select actions** drop down menu. We want to test two:
	- `StopInstances`
	- ![image](https://github.com/user-attachments/assets/93e2b702-ba0f-4b96-96cf-ed2b49791929)
	- `DeleteTags`
	- ![image](https://github.com/user-attachments/assets/a0724e2b-6fd9-4806-b698-b7bdf3c4654a)
- Now click **Run Simulation**
- ![image](https://github.com/user-attachments/assets/6b68d618-ef60-475f-af3f-990c2e8c28b3)
- Here we see both actions were denied.
- ![image](https://github.com/user-attachments/assets/21fe19af-f24f-4aa2-94cb-b73b42f0b065)
- If you noticed, `StopInstances` is denied as well, even though we just stopped our development instance (not the production). Our dev user group can stop the development instance in the EC2 console, so why does it say denied? The action was denied because the simulation resource is "`*`", which means all resources. Note that your development user can only stop EC2 instances with the **Env - development** tag (not all EC2 instances!). To fix this we need to:
	- Expand the `StopInstances` arrow if you haven't already, and in the **ec2:resourcetag/env** field, add **development** to indicate that you want to run the simulation for the instances with that tag.
	- ![image](https://github.com/user-attachments/assets/cd9ad92c-d44a-4060-ac76-f7cd17c02264)
	- Click **Run simulation** again. Now we can see that the policy allows you to stop EC2 instances with a tag of `development`.
	- ![image](https://github.com/user-attachments/assets/2a14599e-96f7-4747-a540-69f074b100d3)
	- **Secret Mission - IAM Policy Simulator** is now complete!

> - Now we no longer have to log into the user's account to test the policy and risk destroying the dev and possibly prod env. Instead we can simulate our configured policies, and if there are errors, we can tweak them to help them function properly. Learning of this tool helps us work with IAM more effectively.

---

## 🗑️ Delete your resources

🛑 **STEPS BELOW:**

- First we need to delete our EC2 instances. Head to your EC2 console, then on the left-hand sidebar, click **Instances**. Now we must terminate our development and production instances by clicking the checkbox to select all instances, and then clicking the **Instance state** drop down menu. From the menu, click **Terminate (delete) instance**.
- ![image](https://github.com/user-attachments/assets/fd1eea4a-cf4a-47cb-b448-4fbda92436c3)
- You'll see a popup where you will again confirm that you want to delete the instances by clicking **Terminate (delete)**.
- ![image](https://github.com/user-attachments/assets/a25e0b4c-273b-4d1c-8a17-aee5045e8533)
- Success! It may take a while to delete.
- ![image](https://github.com/user-attachments/assets/4a4e8ea8-fc6f-4522-afb9-d3a830040c7d)
- Now to delete everything we did in IAM. Head to the **IAM Dashboard**.
- ![image](https://github.com/user-attachments/assets/4631cee5-7e22-48c6-b68d-3e59f87202ad)
- In your IAM console, delete your:
	- **Account Alias**: `realcompany-alias-am`
		- ![image](https://github.com/user-attachments/assets/63d155a2-aa94-4f75-ad57-904ed787bdce)
		- Click **Delete**.
		- ![image](https://github.com/user-attachments/assets/e85e9648-1ecb-426a-8f09-70f0ae9eccc2)
		- Success!
		- ![image](https://github.com/user-attachments/assets/0c5ca3d1-c76c-43a1-8856-b983a90d2f20)
	- **User group**: `realcompany-dev-group`
		- Click on **User Groups** in left-hand panel and it brings you to the right page. Select the group. Click **Delete**.
		- ![image](https://github.com/user-attachments/assets/d5685186-d66f-4b04-956b-cd09be522ef4)
		- A pop up appears. Type in the name of the group in the text field and then click **Delete** again.
		- ![image](https://github.com/user-attachments/assets/55372c3e-54df-4cb8-a268-b087bfe412d8)
		- Success!
		- ![image](https://github.com/user-attachments/assets/05678189-74f7-477d-bfe5-fa5c9d6ac1ab)
	- **User**: `realcompany-dev-intern`
		- Click on **Users** then select our user. Then click **Delete**
		- ![image](https://github.com/user-attachments/assets/3dfa6c3f-90e0-4add-bffe-1e4c5b66be40)
		- A pop up appears. Type in the name of the user in the text field and then click **Delete** again.
		- ![image](https://github.com/user-attachments/assets/bdfcbd2c-2a64-4b31-a5bd-36bdf2b4063f)
		- Success!
		- ![image](https://github.com/user-attachments/assets/9ed3b630-4ee2-476c-be43-f8ac1228fd3b)
	- **Policy**: `RealCompanyDevEnvironmentPolicy`
		- Click **Policies** on the left-hand navigation bar. Find the policy you created by filtering by type to "Customer managed". Select the policy and click **Delete**.
		- ![image](https://github.com/user-attachments/assets/5e723fa5-58a3-4f97-a49d-033ba7059165)
		- A pop up appears. Type in the name of the policy in the text field and then click **Delete** again.
		- ![image](https://github.com/user-attachments/assets/f0179591-4a23-4cd2-af8c-4f4279e728c6)
		- Success!
		- ![image](https://github.com/user-attachments/assets/df3e7a1a-4bbd-4c3c-a56b-223809bcf77b)
- That completes the project!

> - Check out a summary of what we learned and accomplished below!

---

## Summary

This project demonstrated how to use AWS Identity and Access Management to secure cloud resources. Here's a summary:

**Goal:**

- Launched two EC2 instances (production and development environments).
- Onboarded an intern with access to the development environment only.

**Tools Used:**

- **AWS Management Console:** To launch EC2 instances, create IAM users, groups, and policies.
- **EC2:** Amazon's compute service for providing virtual machines.
- **IAM:** AWS's service for managing user identities and access control.
- **JSON:** Used to define IAM policies.
- **IAM Policy Simulator:** A tool to test IAM policies without actually performing the simulated actions.

**Procedure:**

1. **EC2 Instance Launch:** Two EC2 instances were created, one tagged with "production" and the other with "development".
2. **IAM Policy Creation:** A JSON policy was written to grant full access to EC2 resources tagged with "development" and read-only access to all EC2 resources.
3. **Account Alias Setup:** An account alias was created to simplify login URLs.
4. **IAM User and Group Creation:** An IAM user was created and added to a "dev" group. This user will be given the development policy.
5. **Access Testing:** The console was accessed as the new intern user to verify their development permissions and ensure they lacked production access.
6. **IAM Policy Simulator:** The policy was tested using the simulator to confirm its functionality.

**Learnings:**

- Understanding of **IAM users, groups, roles, and policies**.
- How to apply the **principle of least privilege** by restricting access.
- Working with **JSON policies**.
- Launching and managing **EC2 instances**.
- Using **tags** to organize resources.
- Logging in as different users and understanding permission differences.
- Utilizing the **IAM policy simulator** for efficient testing.

By completing this project, you gain a foundational understanding of AWS security and IAM, which is crucial for building and managing secure AWS environments.

---
