
---

# [Build a Security Monitoring System on AWS - CloudTrail, CloudWatch, SNS & More!](https://www.youtube.com/watch?v=wEtYJBSsgks)

---

**Credits**

- This project was inspired by:
	- [NextWork](https://www.youtube.com/@itsnextwork)

---

Learn to build a complete AWS security monitoring system! Follow along as we set up real-time alerts whenever someone accesses your sensitive secrets in AWS.

In this hands-on tutorial, you'll learn how to:
✅ Securely store a secret in AWS Secrets Manager
✅ Set up CloudTrail to track ALL attempts to see your secret
✅ Create CloudWatch metrics and alarms for monitoring
✅ Configure SNS notifications to get emails 
✅ Troubleshoot common issues in your security and monitoring system

🔥 WHY THIS MATTERS:
Ever wondered how to keep tabs on who’s accessing your most sensitive data in AWS? Whether it’s API keys, database credentials, or other critical secrets, it's a security risk every time someone accesses your confidential information. That's why companies invest heavily in robust monitoring systems to track and alert on any unusual activity.

Protecting data is an essential skill for roles like:
- Cloud Security Engineers
- DevOps Engineers
- Cloud Administrators
- Anyone working with AWS who wants to level up their security skills!

- **Key Skills/Tools:** AWS CloudTrail, CloudWatch metric filters, SNS notifications, Secrets Manager

---

## Step 0: Welcome and Project Intro!

- The purpose of this project is to build a Security Monitoring System that creates alerts when sensitive information is accessed.

- **We will use the following AWS tools:**
	- AWS Secrets Manager
		- **Purpose:** Securely store and manage sensitive information like API keys, database credentials, and other secrets.
		- **How we'll use it:** We will demonstrate how to store a dummy secret in Secrets Manager. This secret will be the target of our monitoring efforts.
	- AWS CloudTrail
		- **Purpose:** Tracks user activity and API calls within your AWS account, providing a detailed audit log.
		- **How we'll use it:** We'll configure a CloudTrail trail to specifically log management events, including API activity related to secret access. This ensures that all attempts to view the secret are recorded.
	- AWS CloudWatch
		- **Purpose:** Monitors AWS resources and collects metrics and logs.
		- **How we'll use it:** We'll set up CloudWatch to receive logs from the CloudTrail trail. Then, we'll show how to create a custom metric filter to specifically identify events where secrets are accessed.
	- Amazon SNS (Simple Notification Service)
		- **Purpose:** Sends notifications to users or other AWS resources about events or alarms.
		- **How we'll use it:** We'll configure an SNS topic to receive email notifications. When a CloudWatch alarm is triggered (indicating suspicious secret access), SNS will send an email alert to the subscribed user.
	- Amazon S3 (Simple Storage Service)
		- **Purpose:** S3 is a web service interface that provides object storage, meaning data is stored as objects within "buckets" (containers)
		- **How we'll use it:** CloudTrail logs are stored in an S3 bucket. This is where the long-term, historical records of user activity are kept. CloudWatch logs are also pulled from the same S3 bucket where CloudTrail stores its logs. This allows CloudWatch to access and analyze the logs without a direct integration.
	- AWS CLI (Command Line Interface)
		- **Purpose:** The AWS CLI offers a single, consistent way to interact with all AWS services, regardless of their specific APIs
		- **How we'll use it:** We will use the AWS CLI to manually access the secret stored in Secrets Manager. This generates CloudTrail events that can be verified to ensure the monitoring system is working correctly. The AWS CLI can also be used to trigger CloudWatch alarms manually, for troubleshooting purposes.

- **Prerequisites**
	- An AWS Account

- **Cost**
	- Free

- **Architecture Diagram**
	- ![image](https://github.com/user-attachments/assets/7ed4efff-1dd5-4e18-90d2-88865851836a)

- **Summary**
	- **The goal of the project is to build a system that can monitor access to sensitive secrets in AWS. This is achieved by using the following AWS tools together:**
		1. Secrets are stored securely in **AWS Secrets Manager**.
		2. **AWS CloudTrail** tracks all attempts to access these secrets.
		3. **CloudWatch** receives these **CloudTrail** logs and generates metrics based on secret access events.
		4. A **CloudWatch** alarm is set up to trigger when the number of secret accesses exceeds a predefined threshold.
		5. **AWS SNS** is used to send email notifications to alert users when the alarm is triggered.
	- **Essentially, the system works like this:**
		- Secrets are protected (Secrets Manager).
		- Access is logged.
		- Logs are analyzed for suspicious activity.
		- Suspect activity triggers an alert (CloudWatch Alarm).
		- Users are notified instantly.
	- This system allows for real-time monitoring of secret access, helping to identify and respond quickly to potential security breaches.

**Let's get started!**

---

## Step 1: Create a Secret
> In this step we will set up a secret in AWS Secrets Manager. This is going to be what we will be protecting and monitoring throughout the rest of the project.

- Once you're logged into the AWS Console, navigate to **Secrets Manager**.
- ![image](https://github.com/user-attachments/assets/ab530a4e-3785-4e30-9cdc-f4368d45b650)
- You'll see a screen like image below. Click "Store a new secret".
- ![image](https://github.com/user-attachments/assets/123f6b58-a610-4324-b7f3-63db21d77721)
- Once clicked you'll be brought to a new screen where you will begin the process of choosing what secret you would like to store. There are many secret types (mostly focused on databases), but we will choose "Other type of secret" since we are only storing dummy data.
- ![image](https://github.com/user-attachments/assets/19c5a19a-1944-45ca-b693-f4265278ae2c)
- For the Key/value pairs section, we will stick with the Key/value option over plaintext. The key acts as a label for the secret (EX: username, password, database name, etc.). The actual value of the secret is the value. For this demonstration, we use the pair of 
	- Key - "The Secret is"
	- Value - "Water is wet"
- ![image](https://github.com/user-attachments/assets/44cf8f85-014e-4b9a-8ca6-073f48970773)
- Now we will encrypt the key with the default settings which is through AWS KMS (Key Management Service). This makes sure that no one can find out our secret unless they have the right access. Click "Next".
- ![image](https://github.com/user-attachments/assets/cf89679a-0eae-4cb4-a4c2-a61ad80f0197)
- Now we will give the secret we are creating a name and description.
	- Secret Name - "TopSecretInfo"
	- Description - "We can't let them know the truth."
- ![image](https://github.com/user-attachments/assets/71b67bfb-fa3a-401d-80af-4ce0be0c8a55)
- We will skip the "Tags", "Resource permissions", and "Replicate Secret", but tags are a great way to organize Secrets if you have a lot of them. Resource permissions allow you to control which users, roles, or AWS services can access your secret. Replicate Secret lets you copy your secret to multiple regions so that applications that might need the same secret that live in different types of regions can all have access to that secret because the secrets are regional, so if you're trying to access it from a separate region, you may not be able to see it. Click "Next" to continue.
- ![image](https://github.com/user-attachments/assets/10f82d7a-f069-46fd-9475-7a0b8151a808)
- We will also click "Next" to skip through the "Configure rotation - optional" section. This section is only needed if you want Secrets Manager to periodically automatically replace your credentials (like passwords or API keys) with new values every 30, 60, or 90 days. This limits how long a compromised secret would be useful to an attacker, but we will be keeping our secret static so there is no need.
- ![image](https://github.com/user-attachments/assets/2b8fcb7f-79af-410f-90c2-46883d00e05f)
- Now we are in the "Review" section where we look everything over and make sure it is to our standards. Once you're done reviewing the entire page, click "Store".
- ![image](https://github.com/user-attachments/assets/5fb0aadb-07ed-48ea-86b8-434f86041c6a)
- ![image](https://github.com/user-attachments/assets/0153d224-7d7f-4463-93be-5b38560111c8)
- ![image](https://github.com/user-attachments/assets/3133ebb4-a778-43e2-ac69-0d4ab1c874cd)
- After click "Store" you will be brought here where it tells you to refresh:
- ![image](https://github.com/user-attachments/assets/1f2a6ca1-8e8c-4dcb-83f0-d4a9c80baffa)
- When you refresh you will see your secret. Click on it to view the details.
- ![image](https://github.com/user-attachments/assets/7caa0043-7274-4e1d-af8e-bbe0943f3ead)
- After clicking on your secret you can see all of the general information it holds.
- ![image](https://github.com/user-attachments/assets/e07dec6d-a665-4213-9c43-1b0e19c7656a)
- Now we are complete with **Step 1 - Create a Secret**!

> Now we are ready to move on to **Step 2 - Configure CloudTrail** where we try to track access to the secret.

---

## Step 2: Configure CloudTrail
> Now let's configure CloudTrail to track access to our secret. CloudTrail is a **monitoring** service - it records events that happened in your AWS account, like creating resources, updating a name or setting... and accessing secrets in Secrets Manager

- Search "cloudtrail" in the search bar then click on the name.
- ![image](https://github.com/user-attachments/assets/155d82ec-cab6-410d-9c9a-051b2f3e36f4)
- You will see a screen like the image below, and in the left hand navigation panel, click on "Trails" then close the navigation panel.
- ![image](https://github.com/user-attachments/assets/777ca00d-28ef-416f-bd60-7102b08b99e6)
- Your screen should look similar to the image below. Click "Create trail".
	- **Trail** - A trail in CloudTrail is a record of the events that happen in your AWS account. It's like a folder where CloudTrail stores the logs for the activities you want to monitor.
- ![image](https://github.com/user-attachments/assets/104de73b-199d-4d17-bac1-0027308faabd)
- Now we will begin creating the trail by telling CloudTrail **what** activity we want to track and **where** we want to store the logs that we catch. You should be on a page like this where you can see I already chose a trail name of "secrets-manager-trail", and left the log Storage Location as the default "Create a new S3 bucket". S3 buckets are useful for long term storage.
- ![image](https://github.com/user-attachments/assets/863dd906-9f8b-4fa3-8cb2-655a0a043192)
- On the same page under "Trail log bucket and folder", enter a unique bucket name. Mine will be "secrets-manager-trail-am", with "am" just being my initials.
- ![image](https://github.com/user-attachments/assets/e79dcb7e-1e0d-4f51-912c-07e50f88556c)
- ⚠️ Make sure to uncheck "Log file SSE-KMS encryption". Otherwise you'll get charged for creating a new customer managed KMS key!
	- We will be leaving the other settings as their defaults:
		- **Log file validation (Enabled)** - Log file validation lets you verify that your log files haven't been tampered with after CloudTrail delivered them. CloudTrail creates a digital signature alongside each log file, which you can use later to confirm the logs are authentic and unchanged.
		- **SNS notification delivery (Disabled)** - SNS notification delivery sends you a notification whenever a new log file is delivered to your S3 bucket. This helps you know immediately when new activity has been logged, rather than having to check manually. We'll do this later in our project.
		- **CloudWatch Logs (Disabled)** - CloudWatch Logs integration forwards your CloudTrail logs to CloudWatch Logs in addition to S3. This is super useful because it lets you set up real-time monitoring, create metric filters, and trigger alarms based on specific activities in your logs. We'll also do this later in our project.
		- Tags are optional for organizing but we won't be using it for this project.
	- Click "Next".
- ![image](https://github.com/user-attachments/assets/35e71762-13c5-4ece-b0b3-95cf92d3929c)
- After clicking the "Next" button we are brought to the page where we get to choose the type of log events we would like to collect in our trail. What are the different types of CloudTrail events? CloudTrail captures different types of events, each showing you a different view of what's happening in your AWS account:
	- **Management Events:** These cover administrative actions taken within your AWS account. Examples include:
		- **Resource Creation:** Launching EC2 instances, creating S3 buckets, etc.
		- **Resource Modification:** Updating security group rules, changing EC2 instance settings, etc.
		- **Resource Access:** Actions like retrieving secrets from Secrets Manager (which is the focus of the video).
		- **IAM Management:** Changes to IAM users, roles, and policies, etc.
	- **Data Events:** These are high-volume events related to data transfer and processing. Examples include:
		- **S3 Object Upload/Download:** Transferring files to and from S3.
		- **Lambda Function Execution:** Invocations of Lambda functions.
		- **Database Activity:** Operations performed on RDS databases.
	- **Insights Events:** These are automatically generated by CloudTrail to highlight unusual patterns or potential security risks. Examples include:
		- **IAM Policy Changes:** Significant alterations to IAM permissions.
		- **Resource Access Bursts:** A sudden surge in access to a specific resource.
		- **User Account Creation/Deletion:** Mass creation or deletion of user accounts.
	- **Network Activity Events:** These track network traffic within your AWS environment. They require a separate VPC Flow Logs configuration. Examples include:
		- **Inbound/Outbound Traffic:** Monitoring network connections to and from your VPC.
		- **Traffic Filtering:** Identifying specific network protocols or ports being used.
	- ⚠️ **Important Note:** Management events are **free** to track with a standard CloudTrail trail. Data, Insights, and Network Activity events require paid features or specific configurations.
- AWS does not want to charge people for monitoring their secrets, so our project still falls under the free option of Management Events. This means we leave the "Events" section as default. Also, below "Events" in the "Management events" section's subsection "API activity" we will leave "Read" and "Write" checked and then also choose to check "Exclude AWS KMS events" and "Exclude Amazon RDS Data API events".
	- **Read** - When someone accesses, reads, or opens the secret.
	- **Write** - When someone creates, deletes, edits, or even tries to retrieve the value of the secret. For this project we picked both to learn about both types of activities, but we really only need the **write** activity. Accessing a secret is considered a write activity because of it's importance.
	- **Exclude AWS KMS Events** - We exclude this because it is high volume. KMS is a core service that's involved in encryption and decryption operations for various AWS resources. Excluding these events reduces the significant amount of noise in your logs. KMS events often happen automatically and don't always directly relate to the primary security concerns you might be monitoring. Excluding KMS events helps you focus on events specific to the services you're using in your application, rather than the underlying KMS infrastructure.
	- **Exclude Amazon RDS Data API events** - We exclude this because it is high volume as well. RDS Data API events are generated whenever data is accessed from or written to an RDS database. This can also lead to a large number of noisy log entries. These events often don't necessarily indicate security breaches. They simply reflect the normal operation of your database. Excluding Data API events helps you prioritize logs related to security-sensitive actions, like user authentication or secret access.
- Click "Next" when you're complete.
- ![image](https://github.com/user-attachments/assets/a7c7583d-92ff-4eba-84dc-c75dd06025dc)
- Here we are brought to the "Review and create" page where we have to make sure if everything is correct. If all is well, then click "Create trail".
- ![image](https://github.com/user-attachments/assets/2dbf1d74-7b3a-4679-9bfb-cb09337a8977)
- ![image](https://github.com/user-attachments/assets/a4b51121-3032-41be-9824-b8cc803bf9c6)
- You should see this screen shortly:
- ![image](https://github.com/user-attachments/assets/50f30400-d5aa-49ea-a951-8e77ae8da943)
- Now we are complete with **Step 2 - Configure CloudTrail**!

> Now we are ready to move on to **Step 3 - Generate Secret Access Events** where we expose our secret by opening it and checking if CloudTrail recorded what we did.

---

## Step 3: Generate Secret Access Events
> Now that CloudTrail is set up, let's generate some secret access events. We'll access our secret in a couple of ways to make sure CloudTrail logs these events. Now it is time to verify what we just set up in the first two steps. Can CloudTrail detect it when we access our secret in Secrets Manager?

- Head back to Secrets Manager in the search console
- ![image](https://github.com/user-attachments/assets/703137e1-6b7c-4a22-9105-47599c60b851)
- Click your secret.
- ![image](https://github.com/user-attachments/assets/4fb4115b-2097-42d8-9906-b0fae611375a)
- You'll be brought inside a familiar page from earlier in the project but this time you will click on "Reveal secret value".
- ![image](https://github.com/user-attachments/assets/ac80f3b0-b8f5-458a-a86c-6626b6144872)
- Once clicked, we see the secret is revealed.
- ![image](https://github.com/user-attachments/assets/8b655f53-dae6-46ef-b06a-175b8d9b139d)
- What did revealing the secret do in AWS behind the scenes? The answer is there is an **API call** to AWS Secret Manager to ask "Can you give me the value of the secret I stored?" The API call is named `GetSecretValue`. It's this specific API call that we want to monitor with CloudTrail, because it represents someone reading your secret! So now that we've accessed our secret one way, we can try another method via the AWS CLI. Open AWS CloudShell by clicking on the CloudShell icon in the top navigation bar.
- ![image](https://github.com/user-attachments/assets/d3d32ad6-73e2-42e9-bac1-b1c78caac392)
- Once clicked, a terminal will take up about half of your screen.
- ![image](https://github.com/user-attachments/assets/b5a7ef38-ccd9-4bc3-8a10-53c60dae73c8)
- Enter this command into your terminal `aws secretsmanager get-secret-value --secret-id "TopSecretInfo" --region us-east-2`. ⚠️ It's important that you replace your Secret name (`"TopSecretInfo"`) and Region (`us-east-2`) with your appropriate information you choose for your project. Then press "Enter" on your keyboard to run the command.
- ![image](https://github.com/user-attachments/assets/b963937f-5200-4fc5-979c-dd5e96f5d781)
- Now we have successfully accessed the key through the GUI and CLI. We must now go back to CloudTrail and on the left hand navigation pane, click "Event history".
- ![image](https://github.com/user-attachments/assets/b53ced7a-060d-47f2-ac53-9ba8b2ca944a)
- Please ignore that I am root, which I'm aware is bad AWS security practice 😅 I will be completing an AWS IAM project after this one is complete so that I may rectify this issue. Here is the CloudTrail Event history page:
- ![image](https://github.com/user-attachments/assets/2d6b1413-fcdc-4af2-a448-3409dfe03d2c)
- There are a total of 30 events captured so far, which is not a lot, but in order to look for what we came here for in the first place, we need to filter down the list. We do this by going and clicking the dropdown menu under "Lookup attributes" and then clicking "Event Source" then in the search bar that is next to it, we can look for specific events that involve Secrets Manager. We can type `secretsmanager.amazonaws.com` in the search bar to achieve the results or it will auto-populate the text.
- ![image](https://github.com/user-attachments/assets/4a457cf2-9bfe-44ab-b5cb-c19c8f3430f4)
- We have now reduced the total events from 30 to 16 and at the top we can see `GetSecretValue`, the API call we mentioned earlier that lets us know when someone viewed our secret! We have two invasions of privacy here with one being from the CLI and the other from the AWS console. That confirms that CloudTrail is indeed working and is tracking access to our secrets through the console and in the CLI. This shows how useful of a tool CloudTrail can be for security teams that need to track who accessed what at a certain time. CloudTrail starts collecting Management Events for free the moment you create your account, so we actually have some data from before we even created out trail. 
- ![image](https://github.com/user-attachments/assets/89e0c130-78a1-4c54-a786-ebd89bf6c7af)
-  Now we are complete with **Step 3 - Generate Secret Events**!

> Now we are ready to move on to **Step 4 - Track Secrets Access Using CloudWatch** where we enable CloudWatch logs for our CloudTrail trail and define Metric to track secret access.

---

## Step 4: Track Secrets Access Using CloudWatch

- In this step, we will start using CloudWatch. CloudWatch will help us with doing a deeper analysis with our logs + create metrics to track how many times an event happens. The first thing we need to do is go to left navigation pane in CloudTrail and head to "Trails".
- ![image](https://github.com/user-attachments/assets/7f028c0c-9a33-46c7-8829-735261cd3657)
- Once you're here, click on the Secrets Manager trail you created earlier.
- ![image](https://github.com/user-attachments/assets/5214250b-710f-45d1-92d1-d9e6c3322a01)
- Now you can see the details of the trail in the image below. There is also a section there named CloudWatch Logs. Click on "Edit" within that group.
- ![image](https://github.com/user-attachments/assets/3c507a04-8c14-47f4-8529-615fa8b3d02b)
- It brings you here:
- ![image](https://github.com/user-attachments/assets/be73c451-219b-4a2e-9af3-eb9ef43770cf)
- Before we continue, it's important to get familiar with what we are setting up:
	- **What are Amazon CloudWatch Logs?** - Amazon CloudWatch Logs is a monitoring and logging service that helps you collect, store, and analyze log data from various sources within your AWS environment. CloudWatch Logs can automatically collect logs from AWS services, as well as from applications running on EC2 instances, containers, and other resources. Logs are stored in **log groups**, which are essentially folders for organizing log data. Each log group can contain multiple **log streams**, representing individual sources of logs. CloudWatch Logs provides tools for analyzing log data, including filtering, searching, and charting. This allows you to identify patterns, errors, and security issues. You can set up **metric filters** to process log data and create metrics. These metrics can then be used to trigger alarms, notifying you of important events.
- Now that we understand what CloudWatch Logs are, we're going to click the "Enabled" checkbox to get started with the logging. When you check the box, more choices will pop up for you to decide on.
	- **Log group (New)** - A log group represents a collection of logs from a specific application or service. We're creating a **new** log group to store CloudTrail logs that came from our CloudTrail trail.
	- **Log group name** - Name yours as you please. I will be going with "secretsmanager-loggroup"
	- **IAM Role** - We create an IAM role for CloudWatch Logs to ensure that the service has the necessary permissions to access CloudTrail, but nothing else. An IAM role restricts CloudWatch Logs' access to only the resources and actions it needs. This prevents the service from gaining unauthorized access to sensitive information or performing unintended operations
	- **Role name** - We will name it "CloudTrailRoleForCloudWatchLogs_secrets-manager-trail".
- Click "Save changes" to continue.
- ![image](https://github.com/user-attachments/assets/75750ae2-77d8-425e-ada8-3e8d326c8681)
- We're now going to navigate to CloudWatch.
- ![image](https://github.com/user-attachments/assets/6772fb98-9c6a-4120-baf0-84a5bdd4dfbe)
- This is what the CloudWatch page looks like (image below). We need to select "Logs" in the left navigation pane, and then "Log groups".
- ![image](https://github.com/user-attachments/assets/4f4626cf-a0fb-4588-a12a-d125ee8cb973)
- Here we are presented with the "Log groups" page. Click into your log group.
- ![image](https://github.com/user-attachments/assets/195bdb03-c67f-40d5-af5f-ff24ac116f82)
- Once clicked, you'll see this page which shows your Log streams. A **Log Group** is a container that holds multiple **log streams**, usually associated with a single service or application. This helps to organize and manage logs. A **Log Stream** is a sequence of log events from a single source, like an application instance or a specific AWS service. For example, an EC2 instance might have a log stream for its application logs and another for its system logs. You might see multiple Log streams (i.e subfolders of log groups). Pick any of them. If you only see one, that's fine as well. In my case, I have 4 and will click on the top one `009245113520_CloudTrail_us-east-2_4`.
- ![image](https://github.com/user-attachments/assets/1be37861-a2bd-4a61-b3e9-f9fc521b8491)
- Once clicked, you're brought to all of the **Log events** within the Log stream that you clicked. A **Log Event** is the actual text data generated by an application or service, along with a timestamp. If you don't see rows and rows of logs straight away, you might need to wait a few minutes and refresh your page first.
- ![image](https://github.com/user-attachments/assets/0d07060f-cc94-4ffb-a6b0-3f8c951366e3)
- You can click into events to see more information on what was collected. Looking into the log at the top I can see that the event name was "AssumeRole", and it tells me what time this event was created as well as more helpful information. With this, we have now confirmed that we can see the logs from CloudTrail are now inside of the CloudWatch Log group. This verifies the two services are connected. 
- ![image](https://github.com/user-attachments/assets/14f6bea5-207a-4792-93a8-fe8fd59b98e2)
- **A quick reminder on why we are doing this:**
- Yes, you could see the same events in CloudTrail Event History, which is great for quick investigations on recent events. However, sending logs to CloudWatch Logs is great for us because:
	- CloudWatch Logs is where you can set up **alerts** and **automated responses** when specific events happen, which we'll start setting up shortly.
	- CloudTrail Event History only keeps events for **90 days**, while CloudWatch Logs can store them for as long as you'd like.
	- CloudWatch Logs has powerful filtering tools that let us focus on exactly the events we care about.
- Now that we have that explained, let's move on to creating a **Metric Filter** by heading back to our log group (you can back arrow icon in your browser or click on the log group directly in your console). Once you're there, click the "Actions" drop down menu and then click "Create metric filter".
- ![image](https://github.com/user-attachments/assets/158ce8cd-d54d-4e33-b1e9-97aa1100e23d)
- You should now be on the "Define pattern" page. This is where we will tell CloudWatch about the kind of actions we're looking for.
- ![image](https://github.com/user-attachments/assets/f83bb825-fbbc-4573-96c2-8c92590cb98f)
- First, we need to create a "Filter pattern" (Specify the terms or pattern to match in your log events to create metrics). In this space we will put in the API call we keep mentioning `"GetSecretValue"` (leave in the quotes). If you remember, this will let us know when our secret is accessed in Secrets Manager. With this now as our Filter pattern, we can now filter for all logs that mention `"GetSecretValue"`.
- ![image](https://github.com/user-attachments/assets/aa40e745-003a-4da2-8885-0d208644fea4)
- For the "Test pattern" section, we will get into that in step 6 of the project, but for now just know that this is for making sure that the filter actually works. Right now we are just creating the filter itself. Click "Next" when you're done.
- ![image](https://github.com/user-attachments/assets/a3afe497-42e4-4d8b-a8cd-06e9231ebccd)
- We're brought to the "Assign metric" page. In the "Create filter name" section, insert the name "GetSecretValue" (no quotes).
- ![image](https://github.com/user-attachments/assets/1d6cfccb-d4b1-4632-a230-1c5684d7ff1a)
- Scroll down a bit on the same page to "Metric details". Within it, for "Metric namespace" type in "SecurityMetrics".
	- **Metric namespace?** - A metric namespace is like a folder for your CloudWatch metrics. Namespaces help you organize your metrics and prevent naming conflicts with metrics from other AWS services or applications. We're creating a namespace called "SecurityMetrics" to group all our security-related metrics. The name is also broad enough so that any other security metrics can also utilize this namespace as well.
- Below that for "Metric name", we will enter "Secret is accessed".
	- **Metric name** - Metric name identifies this metric, and must be unique within the namespace. We named it "Secret is accessed", so that when we get an email, or try to filter out through other metrics, it's very clear to us that this metric is about how many times the "GetSecretsValue" event happens.
- For "Metric value" enter "1".
	- **Metric value** - This is what gets recorded when our filter spots a match in the logs. We're setting it to 1 so that each time someone accesses our secret, the counter increases by exactly one.
- For "Default value" enter "0".
	- **Default value** - This is what gets recorded when our filter doesn't find any matches during a given time period. We're setting it to 0 so that time periods with no secret access show up as zero on our charts, rather than not showing up at all. This gives us a complete picture - we can see both when access happened AND when it didn't.
- Skip the "Unit - optional" section and click "Next".
	- **Unit** - How your metric gets displayed. if we were to use one, then we may choose "Count" but it is optional and not needed for our project.
- ![image](https://github.com/user-attachments/assets/92caa6cd-42f8-4eaf-b403-431cf8f7d976)
- Now you are on the "Review and create" page. Look to see if everything is OK and then click "Create metric filter".
- ![image](https://github.com/user-attachments/assets/a3e91c52-4c3a-4ff9-92e4-ecb8e1ec6799)
- You should now see a green banner at the top confirming that the metric filter has been created.
- ![image](https://github.com/user-attachments/assets/82390618-c8bc-42fb-809b-8aeba1ba7970)
- Now we are complete with **Step 4 - Track Secrets Access Using CloudWatch**!

> Now we are ready to move on to **Step 5 - Create CloudWatch Alarm and SNS Topic** where we create a CloudWatch Alarm that triggers when our `SecretIsAccessed` metric exceeds a threshold. We'll also set up an SNS topic to receive email notifications when the alarm is triggered.

---

## Step 5: Create CloudWatch Alarm and SNS Topic

- In this step, we'll use **CloudWatch Alarms** and **Amazon SNS** to create a robust notification system for when our secret is accessed in AWS Secrets Manager.
	- Here's how they work together and how we'll use them:
		- **CloudWatch Alarms:**
			- **Purpose:** Monitor CloudWatch `SecretsIsAccessed` metric for changes or thresholds that might indicate a security issue or other problem.
			- **Functionality:** Set up alarms to automatically trigger when the metric exceeds a specified value, falls below a certain value, or exhibits other unusual behavior.
			- **Integration:** Alarms can be integrated with other AWS services, including Amazon SNS, to send notifications.
		- **Amazon SNS:**
			- **Purpose:** A messaging service for distributing notifications throughout your AWS environment or to external systems.
			- **Functionality:** Publish messages to SNS topics, which can be subscribed to by email addresses, mobile devices, functions, and more.
			- **Flexibility:** Supports various notification types (email, SMS, push notifications, etc.) and allows for custom message formats.
- Let's get started! Still in your CloudWatch Metric filters page, check the box next to the `GetSecretValue` metric filter. Then you will see an option above and to the right of it that says "Create alarm". Click that button and we can then proceed with creating an alarm.
- ![image](https://github.com/user-attachments/assets/8beb3d36-8574-4dd1-8bab-71ef5e3f8838)
- You will be brought to the "Specify metric and conditions" page. Within the page, there is a section called "Metric". Within that section we will select the following options:
	- **Metric name** - Secret is accessed
	- **Statistic** - Average
		- **Statistic** tells CloudWatch how to analyze the CloudWatch metric. In this case, we're saying that we're looking for the average number of times the secret is accessed.
	- **Period** - 5 minutes
		- **Period** is how often CloudWatch checks in on your metric - we've chosen 5 minutes. You can think of it as checking every 5 minutes to see if anyone accessed the secret during that time.
- ![image](https://github.com/user-attachments/assets/1f6438ae-7e59-4534-b743-ef6735b4dd62)
- Scroll down to the "Conditions" section. This is where we decide the conditions that need to be in place in order to tell when CloudWatch Alarm should be alerted. Within this section we will select the following options:
	- **Threshold type** - Static
	- **Whenever Secret is accessed is...** - Greater/Equal
	- **than…** - 1
- The alarm threshold is **when** the alarm should trigger. We're setting up a static threshold  so that your alarm goes off when the `SecretIsAccessed` metric is greater than or equal to `1` in a 5-minute period. It's easy for this alarm to go off, which is great for high-security scenarios! In a real production environment, you may adjust this depending on the sensitivity of your secret. Also, take a look at the graph and see how it has changed based on our setup. Click "Next" when finished.
- ![image](https://github.com/user-attachments/assets/fd283f29-680c-4304-a3ab-101abe03ffcf)
- We are now on the "Configure actions" page. This is how we tell CloudWatch what to do when we want to be alerted. Let's look under the "Notification" section.
	- **Alarm state trigger** - Keep the default setting "In alarm". This tells what should our alarm do when we actually do reach that threshold. It says that we do want to be alarmed.
	- **Send a notification to the following SNS topic** - Here we will select "Create new topic" since this is new. Selecting to create a new topic makes a few more options pop up.
		- **Create a new topic…** - We'll call it `SecurityAlarms`.
			- **SNS Topic** - An SNS topic is like a broadcast channel for distributing messages in Amazon SNS. Think of it as a newsletter where you can send messages and have subscribers receive them. Topics allow you to send the same message to multiple subscribers simultaneously. Subscribers can choose different communication methods (email, SMS, push notifications, etc.) and filter messages based on attributes. SNS handles the delivery infrastructure, ensuring reliable and scalable notification distribution. You can attach metadata to messages using message attributes, providing additional context for subscribers.
		- **Email endpoints that will receive the notification…** - Place in your email address that you want to receive the notification with.
- Once you're done, click "Create topic" and then scroll down to the remaining sections.
- ![image](https://github.com/user-attachments/assets/c8212a87-2d00-46fb-b6ec-fda3aac4503a)
- The following sections are other different types of ways you can respond when the alarm goes off.
	- **Lambda action** - Do you want us to run a random lambda function when the alarm goes off?
	- **Auto Scaling action** - Do you want to auto scale any instances?
	- **EC2 action** - Do you want an EC2 action to trigger?
	- **Systems Manager action** - Do you want an Systems Manager action to trigger?
- We won't be dealing with any of these and are free to continue on by clicking "Next".
- ![image](https://github.com/user-attachments/assets/6731495b-d666-48d9-9449-40f80e860ffa)
- We're now on the "Add name and description" page. Under "Alarm name", enter `Secret is accessed`. Under "Alarm description - optional", enter a description like `This alarm goes off whenever a secret in Secrets Manager is accessed.` Then click "Next".
- ![image](https://github.com/user-attachments/assets/b0df67ff-82be-4498-a074-d814b22da715)
- After clicking "Next", we're brought to the "Preview and create" page where will will make sure everything is up to standards, and then click "Create alarm" when done.
- ![image](https://github.com/user-attachments/assets/8bd98342-d28c-402b-b454-ce547beabfe4)
- ![image](https://github.com/user-attachments/assets/9144ee48-bfab-41dd-aabc-2eee20ba0eaa)
- ![image](https://github.com/user-attachments/assets/8c9840c7-d5d8-4878-a99c-076dbaaa0ab0)
- Once created, you'll see this page which gives you a warning. We have the alarm created and we also have a subscription that needs confirmation. Think of a **subscription** as an email address that will receive the messages you publish to your SNS topic. However, AWS is not going to just let you automatically send emails to anyone you put on the subscription. The person on the receiving end needs to confirm that they would like to receive the emails.
- ![image](https://github.com/user-attachments/assets/83405bb8-43bd-456a-bc4c-60ccd7601ee2)
- If we inspect the warning further we see what it requires. We need to confirm we want to receive the emails.
- ![image](https://github.com/user-attachments/assets/00f1e3d0-fffe-43a1-b651-7498d2d61eae)
- Now we will head over to our email inbox and click on the email that should be named something like "AWS Notification - Subscription Confirmation". Then click "Confirm subscription".
- ![image](https://github.com/user-attachments/assets/fc652568-511b-4275-ad28-3d90c1fc8064)
- Once clicked, you'll see this confirmation screen.
- ![image](https://github.com/user-attachments/assets/c5f14951-b544-4bfb-9c44-baa08d875939)
- Now we are complete with **Step 5 - Create CloudWatch Alarm and SNS Topic**!

> We're ready to move on to our **Secret Mission: Apply your Solutions Architecture Skills**, where we get curious as to if there is a simpler way to have done this without as many different services being involved, and to our surprise there is a way! This way will test out Solution Architect skills. The mission is to remove CloudWatch and alarms from the solution, and instead, configure **direct CloudTrail notifications** and compare that against using CloudWatch and alarms. This challenge will build our **critical thinking** skills around how architecture decisions (like using CloudWatch) are made when building cloud solutions (like this monitoring system).

---

## Secret Mission: Apply your Solutions Architecture Skills

- So CloudTrail can send emails directly to you via SNS, and that will skip the entire CloudWatch setup. Emails will come through SNS, and SNS will send the notification to you. To start the mission, we will head to CloudTrail once again (search it in the search bar if you need help navigating to it). Once you're there, look in the left hand navigation pane and click "Trails"
- ![image](https://github.com/user-attachments/assets/6f6cc870-6ee1-4a82-9f26-5e17c1e01caf)
- Click on our trail.
- ![image](https://github.com/user-attachments/assets/2540aef3-3de4-489b-910c-ed52024a824e)
- Once inside, click the "Edit" within "General details".
- ![image](https://github.com/user-attachments/assets/4b9e73dc-2fca-4609-be0d-3912598aa466)
- Now if you look at the bottom of the page, you will see "SNS notification delivery" as an unchecked box.
- ![image](https://github.com/user-attachments/assets/75162909-410c-4f14-86ee-8461b3961077)
- Check the box to enable a direct connection between CloudTrail and SNS. This brings out another option for us to choose to "Create a new SNS topic", but we will choose our "Existing" one of `SecurityAlarms`. Now we will get notified when CloudTrail has something to send to you as well. When done, click "Save changes".
- ![image](https://github.com/user-attachments/assets/13da624b-72cc-401d-a698-825bc6324975)
- We're brought back to our trail page. Now that we have enabled SNS notifications, we can see it in our "General details". That confirms to us that we are going to be receiving emails directly from CloudTrail now.
	- **What does SNS notification delivery do in CloudTrail?** - When you enable SNS notification delivery in CloudTrail, it will send a notification to our SNS topic **every time** a new log file is delivered to your S3 bucket. This is different from your CloudWatch Alarm, which only triggers when a specific pattern (like accessing your secret) is detected in the logs. In other words, SNS notification delivery does not care about whether or not the event is accessing a secret, it's just going to tell you about Every..Single..Event.
- ![image](https://github.com/user-attachments/assets/7114ce03-6c7e-4554-a554-8abab8d076ce)
- Now that we have saved it, we're going to head back to Secrets Manager. We're going to access our secret one more time and see if we get an email. Click on the secret.
- ![image](https://github.com/user-attachments/assets/995b147d-b0e1-4a15-9664-e031ead053ad)
- Now click "Retrieve secret value".
- ![image](https://github.com/user-attachments/assets/a715232c-65c7-4662-bbc8-d0a539375dfa)
- The secret is revealed once again.
- ![image](https://github.com/user-attachments/assets/f3690c73-157a-47d9-807e-d6201884496a)
- It took a few minutes to show up in my email and sadly it does not provide much information. It basically says that there is a new log in the S3 bucket, but not what's inside the log. Also, I am receiving a lot of emails that are most likely unrelated to accessing the secret from Secrets Manager, as I have only revealed it once. This shows that CloudWatch would be the better option for notifications, but that doesn't mean that this method does not have it's use cases in production environments.
- ![image](https://github.com/user-attachments/assets/dc7a2afc-bf44-4903-853c-86630fce6225)
- Now we need to learn how to turn off the alerts so we don't get our emails flooded with notifications. Head back to the CloudTrail console, select your Trail again, and click the "Edit" button again. Uncheck the "SNS notification delivery" checkbox. Then click "Save changes".
- ![image](https://github.com/user-attachments/assets/dd6e989d-86ba-4644-9937-89c5ae16be68)
- Now we can see it is Disabled again.
- ![image](https://github.com/user-attachments/assets/701ecd58-6a56-4415-92ba-f42da9e6513d)
- Now we are complete with **Secret Mission: Apply your Solutions Architecture Skills**!

> We're ready to move on to **Step 6: Test Email Notification**, where we finally test our ENTIRE monitoring system by accessing our secret one more time, then verifying whether we get an email. If it doesn't, then we will troubleshoot what to do next.

---

## Step 6: Test Email Notification

- Head back to Secrets Manager. Let's try and trigger our own alarm by retrieving the secret value again.
- ![image](https://github.com/user-attachments/assets/a7fb5b01-3b5b-4d1c-bb60-4ccddc3d18f1)
- What do you think is going to happen now? Are you going to get the alert email about your secret getting accessed? Check your email after a few minutes....
- A few minutes pass by and we still didn't get our email which means we have some troubleshooting to start. If you noticed in the secret mission, we did not get an email from CloudWatch during that test either... only with CloudTrail SNS notification delivery. 
- Let's retrace our steps. Do we think CloudTrail actually recorded the event? What if CloudTrail missed it, and didn't know you retrieved your secret's value?
- ![image](https://github.com/user-attachments/assets/99abf1f8-5724-45aa-a659-6718e4631dc2)
- Head back to CloudTrail Event History.
- ![image](https://github.com/user-attachments/assets/2fcd2e81-d1d3-40bc-a05d-2f9c7e0ffbe0)
- Select "Event source" for the drop down menu and `secretsmanager.amazonaws.com` for the search bar. As you can see, the `GetSecretValue` event is being recorded, which means it is working. It has passed our check ✅
- ![image](https://github.com/user-attachments/assets/e0c8111f-6e01-4bd2-b4c6-d54bdfe2b7b1)
- Our next step in the architecture is to see if maybe CloudTrail did something to CloudWatch.
- ![image](https://github.com/user-attachments/assets/8be79b4f-c20c-42bb-8372-acef5bf793eb)
- Navigate back to CloudTrail and select Trails in the left navigation pane.
- ![image](https://github.com/user-attachments/assets/bda773fa-890f-4fce-bb8f-f36489918cad)
- Click into your Trail and within "General details" check the "Last log file delivered" timestamp. It should be recent if log files are being delivered properly. In my case, mine is recent.
- ![image](https://github.com/user-attachments/assets/66f3d55c-bd9e-4b1a-b4f8-25abec393541)
- Also, check if the log group name is correct and the IAM Role has permissions to write to CloudWatch Logs. It seems that everything is correct here and it has also passed our test ✅
- ![image](https://github.com/user-attachments/assets/e582e139-e001-4c6d-b1f0-023fdcc82dca)
- So far in our Architecture we have checked 2 parts out of 5 possible error areas:
	- **CloudTrail** - Passed ✅
		- CloudTrail **IS** recording the `GetSecretValue` event.
	- **Log Delivery** - Passed ✅
		- CloudTrail **IS** sending logs to CloudWatch.
	- **Metric Filter** - Not Checked ❔
		- Is CloudWatch's **metric filter** filtering logs correctly?
	- **Alarm Configuration** - Not Checked ❔
		- Is CloudWatch's **Alarm** triggering an action?
	- **SNS Subscription** - Not Checked ❔
		- Is **SNS** delivering emails to you?
- We need to move on to see if the issue is in **CloudWatch Metric filter**. Maybe our filter is accidentally rejecting the event where our secret is accessed.
- ![image](https://github.com/user-attachments/assets/1ae280a9-75d8-4be5-ac2c-cb8f1ed4f2da)
- Head to CloudWatch and in the left navigation pane, click the "Logs" drop down menu and click "Log groups".
- ![image](https://github.com/user-attachments/assets/1d22b947-5231-435d-b9c2-23f0dfc5015e)
- Click on the log group and you'll be brought to it's page. Notice the "Metric filters" tab towards the bottom. We'll need to click on that.
- ![image](https://github.com/user-attachments/assets/d9679b56-8d0c-435e-b62a-61484e0ab64e)
- When you're here, select the checkbox on the filter, and then select "Edit".
- ![image](https://github.com/user-attachments/assets/5e2376af-5177-4e26-8563-ebbc7262f69a)
- We're back in the creation of our filter, and now we are going to try out the "Test pattern" section to see whether or not it can accurately grab the event where you're accessing your secret.
- ![image](https://github.com/user-attachments/assets/bbb77e28-c812-4ba5-92bc-218a2139706a)
- I don't have access to the test log data so I watched the tutorial to see what results the presenter had. However, I will go over what was in the test data.
	- **What is in the test data?** - The block of text contains sample CloudTrail log entries that simulate various activities related to AWS Secrets Manager. Each entry represents a different API action that might occur when interacting with your secrets.
	- The data is formed in JSON and includes multiple event records showing different operations like `GetSecretValue` (accessing a secret), `DescribeSecret` (viewing secret metadata), `CreateSecret` (creating a new secret), and other common Secret Manager actions.
	- When you paste this into the test field, CloudWatch will analyze it using your filter pattern and highlight which events match your criteria (in this case, looking for `GetSecretValue` events).
- I actually decided to generate some of my own test data using ChatGPT and it gave me this to place in:
```
{"eventTime": "2025-04-04T12:34:56Z", "eventSource": "secretsmanager.amazonaws.com", "eventName": "GetSecretValue", "awsRegion": "us-east-1", "sourceIPAddress": "192.168.1.1", "userAgent": "aws-cli/2.9.0", "requestParameters": {"secretId": "arn:aws:secretsmanager:us-east-1:123456789012:secret:MySecret"}}
{"eventTime": "2025-04-04T12:35:10Z", "eventSource": "secretsmanager.amazonaws.com", "eventName": "DescribeSecret", "awsRegion": "us-east-1", "sourceIPAddress": "192.168.1.2", "userAgent": "aws-sdk-java/1.12.200", "requestParameters": {"secretId": "arn:aws:secretsmanager:us-east-1:123456789012:secret:AnotherSecret"}}
{"eventTime": "2025-04-04T12:36:20Z", "eventSource": "secretsmanager.amazonaws.com", "eventName": "CreateSecret", "awsRegion": "us-east-1", "sourceIPAddress": "203.0.113.45", "userAgent": "boto3/1.24.0", "requestParameters": {"name": "NewSecret", "secretString": "SuperSecretValue"}}
{"eventTime": "2025-04-04T12:37:30Z", "eventSource": "secretsmanager.amazonaws.com", "eventName": "UpdateSecret", "awsRegion": "us-east-1", "sourceIPAddress": "198.51.100.23", "userAgent": "aws-sdk-go/1.44.0", "requestParameters": {"secretId": "arn:aws:secretsmanager:us-east-1:123456789012:secret:MyUpdatedSecret"}}
{"eventTime": "2025-04-04T12:38:45Z", "eventSource": "secretsmanager.amazonaws.com", "eventName": "DeleteSecret", "awsRegion": "us-east-1", "sourceIPAddress": "203.0.113.99", "userAgent": "aws-sdk-python/1.22.0", "requestParameters": {"secretId": "arn:aws:secretsmanager:us-east-1:123456789012:secret:OldSecret"}}
```
- Here is the result:
- ![image](https://github.com/user-attachments/assets/e1528278-1ee3-4bc6-9bdb-8e0b0ef80498)
- As we can see it worked and the pattern is only grabbing `"GetSecretValue"` out of the 5 dummy test data. If you're curious on NextWork's results, here they are with similar successful results:
- ![image](https://github.com/user-attachments/assets/5cc8f27e-7ba4-43d7-a487-8a15ec1ca9c9)
- With this, we confirm that CloudWatch's metric filter **IS** filter logs correctly ✅
- Now we need to check CloudWatch Alarms. Maybe the alarm isn't getting triggered and turning into an SNS notification to send out. Let's troubleshoot this by triggering the alarm manually.
- ![image](https://github.com/user-attachments/assets/02fc44a8-df6a-44db-a6f7-46d9a1d310f4)
- Open AWS CloudShell and ask it first **how** to trigger our alarm manually by running the command `aws cloudwatch set-alarm-state help`. Once run it your output will look like this:
- ![image](https://github.com/user-attachments/assets/abeacf86-8d7a-435d-bd52-345ef83f8b5c)
- Scroll down using the arrow keys on your keyboard to see the rest of the help page. Keep heading down until you see "SYNOPSIS". This will show us the exact syntax we need  along with it's required parameters:
	- `--alarm-name <value>` - The name of the CloudWatch alarm you want to trigger
	- `--state-value <value>` - Change state to ALARM, OK, or INSUFFICIENT_DATA
	- `--state-reason <value>` - Tell CloudWatch why you're changing the state
- ![image](https://github.com/user-attachments/assets/fe5b4985-24bc-4c35-9ff1-6cdfac03baf5)
- Exit here by pressing `q` on your keyboard. Then enter this into your terminal.

```
aws cloudwatch set-alarm-state \
--alarm-name "Secret is accessed" \
--state-value ALARM \
--state-reason "Manually triggered for testing"
```

- ![image](https://github.com/user-attachments/assets/e18ee26e-ced2-45a9-8d93-af81e20e8be7)
- The commands have now been executed. Let's see if we get an email. There should be a notification email that your alarm is in alarm, and "Manually triggered for testing" as the reason. It looks like we received an email!
- ![image](https://github.com/user-attachments/assets/de7332dd-0022-44b4-bda2-c6be8fb84ba8)
- So we know that the CloudWatch Alarm can trigger SNS and send an email, so maybe it's just not being triggered. We're about to go down into our final investigation. We'll go back to CloudWatch and in he left navigation pane click the "Alarms" drop down menu and then click "All alarms".
- ![image](https://github.com/user-attachments/assets/ae15b041-9f53-49db-88f3-dcef99b4ceb9)
- Then select your alarm in the checkbox, and then look above to the "Actions" drop down menu. Click it and then click "Edit".
- ![image](https://github.com/user-attachments/assets/3422683b-b56d-4883-b2cb-2bc270d2139d)
- On this page click on the graph to enlarge it so we can see the anomalies. Here we get a closer look at what may be the issue. The alarms are being triggered, but the amount is not reaching the threshold of `1` and is instead reaching only `0.1`.
- ![image](https://github.com/user-attachments/assets/54a1c749-1cfc-4606-b620-b1d35a4efe24)
- The issue is that our "Statistics" are set to "Average"! It should be instead set to "Sum". Here's why:
	- **Sum** adds up **all** occurrences of secret access in the period (what we want).
	- **Average** would calculate an average rate (i.e the average number of times our secret was accessed per second over the 5 minute period), which might not cross our threshold.
- Now that we got that settled, change the "Statistic" dropdown from "Average" to "Sum".
- ![image](https://github.com/user-attachments/assets/5eb7d464-8856-47df-bb9d-220134a093d8)
- You can also update the "Period" from "5 minutes" to "1 minute", so we can trigger the alarm even faster when we see the Secret's value.
- ![image](https://github.com/user-attachments/assets/43b7d862-120f-4978-876f-d33e3b03df72)
- Now that we're done, click "Skip to Preview and create" at the bottom of the page.
- ![image](https://github.com/user-attachments/assets/a955331d-4e2b-403a-8ea8-eba27a7e72cc)
- Scroll down to the bottom of the page again and click "Update alarm".
- ![image](https://github.com/user-attachments/assets/55539cec-a322-4420-a6ca-e716a767f90e)
- Now we know that CloudWatch Alarms threshold configuration was the issue, but we still would like to check SNS email delivery to make sure that was fine as well.
- ![image](https://github.com/user-attachments/assets/4c9a9d51-2b04-4be3-85dd-b3aaf2de665e)
- Search for SNS in the search bar and click on it's name.
- ![image](https://github.com/user-attachments/assets/ba89a7d1-2ac2-4a3d-ab4d-8fae5e6c9010)
- Here is the page for SNS. Click on "Topics" in the left navigation pane.
- ![image](https://github.com/user-attachments/assets/cf24dbec-28a0-4e61-9a1b-113e46d383eb)
- Here we see our topic `SecurityAlarms`. Select it and then click "Publish message".
- ![image](https://github.com/user-attachments/assets/3862965d-00d2-42ac-9198-1ab12c2b7963)
- You're brought to the "Publish message to topic" page.
- ![image](https://github.com/user-attachments/assets/a3e6a89c-5d0e-4015-be4d-5484d2aef20b)
- In the "Message details" section, under the "Subject - optional" subsection, enter `Testing`. In the "Message body" section, and under the "Message body to send to the endpoint" subsection, put whatever you want, but I will put a simple `Wassup`.
- ![image](https://github.com/user-attachments/assets/cf34206a-1251-4174-a0d9-31b856235951)
- Leave everything else as default. Message attributes is just for metadata if you want to include it for easier organization. Scroll down to the bottom and click "Publish message".
- ![image](https://github.com/user-attachments/assets/3064aa09-c986-4865-8b5f-98aa38d759d3)
- You should see a green confirmation banner at the top of your page.
- ![image](https://github.com/user-attachments/assets/5fad3981-8fcd-4e5d-aa3b-bbaf4aa15c15)
- Check your email for the `Wassup` message. Looks like it is there, confirming that SNS is working ✅
- ![image](https://github.com/user-attachments/assets/19d07247-f5a5-47c4-bed0-bb1153b710c7)
- With that troubleshooting is now complete! With the investigation over, let's go ahead and access our secret one more time to validate! Reveal the secret and see if you get an email.
- ![image](https://github.com/user-attachments/assets/0334c93d-8ab8-4e76-9737-ce2bbd1cf258)
- While you wait for your email, check in your CloudWatch alarm to see if it is going off. Here we can confirm that it is **In Alarm** mode after about 2 minutes. For some reason the spike on the graph is not showing for me but instead shows up as a small blue dot. Regardless, it is still there.
- ![image](https://github.com/user-attachments/assets/d0ddfa9e-02a8-4fc2-b008-9e4f23a9f4e1)
- I went into another graph from where we go to edit the alarm, and it gave a better view. You can actually see the spike and where it touches the threshold.
- ![image](https://github.com/user-attachments/assets/d822de01-57a0-4a9a-9e06-798bfe6f0f5e)
- Now that some time has passed, it shows better on the main graph as well.
- ![image](https://github.com/user-attachments/assets/d528dc0e-5d0e-4e21-99c8-46031a0284cf)
- *(Note - Don't forget you can change the time periods to get better views of the graph by zooming in or zooming out of a specific time period.)*
- ![image](https://github.com/user-attachments/assets/9f039e34-e2c7-4334-b309-d7089da7fd48)
- Going to the emails, we can see we have been alerted from there.
- ![image](https://github.com/user-attachments/assets/cb3a3b07-83b9-4af7-ba19-2e3b85d0fc6b)
- Our Security Monitoring System is working! 🎉
- Now we are complete with **Step 6 - Test Email Notification!

> We're ready to move on to **Before you go: Delete your resources**, where we clean everything up so that we don't get charged.

---

## Before you go: Delete your resources

- Head to S3 so that we can delete everything in the bucket, which will be an assortment of logs.
- ![image](https://github.com/user-attachments/assets/b7f772f1-8427-453b-898c-a6b71004f8c8)
- Here is the S3 main page. We'll delete the logs and then the bucket itself. 
- ![image](https://github.com/user-attachments/assets/a9648486-2f61-420c-ad8f-b07d8777df9f)
- Select your bucket (don't click on the name) and click "Empty" in the above selections.
- ![image](https://github.com/user-attachments/assets/77be58cc-07ee-4242-92c8-7c7950578348)
- You'll get a warning and be prompted to type `permanently delete` before clicking "Empty".
- ![image](https://github.com/user-attachments/assets/33c628b1-c126-4dc0-bea8-df342b70d025)
- Once done, you'll receive a confirmation:
- ![image](https://github.com/user-attachments/assets/c4b494f8-f7a1-4c23-8fae-dd766b67629a)
- Now we will delete the actual bucket. Head back to S3 by clicking "Exit". Then select the bucket again and click "Delete" from the upper selections. Once you do that then you'll be brought to this screen where another warning will pop up and this time ask you to input the name of your bucket `secrets-manager-trail-am`. Enter it and then click "Delete bucket".
- ![image](https://github.com/user-attachments/assets/b898314e-01d7-42d3-a709-e13eb23fe633)
- And with that we have successfully deleted our S3 bucket.
- ![image](https://github.com/user-attachments/assets/235fc253-f825-4c26-a4e6-e64f24a3175c)
- Now we must move on to CloudTrail. head there and then head to Trails. Click on your Trail, and then click the "Delete" button in the top right corner. 
- ![image](https://github.com/user-attachments/assets/ee947202-cc70-4a95-ae6c-6030715179d3)
- You will receive a warning. Click "Delete" again.
- ![image](https://github.com/user-attachments/assets/38ce025e-165c-4654-bf44-f7616e394a94)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/cb6c5f09-47f7-4862-b33f-e683f5123f32)
- Let's head to CloudWatch. Click on "Alarms" in the left navigation pane, then "All Alarms". You'll see your alarm. Click into it. Then click "Actions" and click "Delete".
- ![image](https://github.com/user-attachments/assets/efdc2ad2-99b7-4b52-ae31-af2baa5708a6)
- Confirm the deletion by clicking "Delete" again.
- ![image](https://github.com/user-attachments/assets/a9b231ce-2b1f-4f17-a2a6-05a7b6d8f6be)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/522b8b0d-46c3-4287-ba91-37daa3b408ae)
- The alarm is deleted. What's next is for us to delete the Log group. Click "Logs" in the left navigation pane, then "Log groups". Select your log group and then click the "Actions" button, and finally the "Delete log group(s)" option.
- ![image](https://github.com/user-attachments/assets/d05f7168-4d3f-4977-be59-8916968b3543)
- Then click "Delete" again.
- ![image](https://github.com/user-attachments/assets/3195d468-39e4-4c49-9c00-d16a6757c645)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/4d16f19b-5030-4628-b30e-e1f61f1e2cf5)
- Now we need to go to Secrets Manager. Once there, click into your secret. Click the "Actions" button in the the "Secret details" section. Click the "Delete secret" option.
- ![image](https://github.com/user-attachments/assets/ee52d407-77c7-44a4-b4eb-f1094ad1f683)
- You're now brought up with a warning saying that Secrets Manager doesn't delete the secret right away, and instead waits 7 days, just in case you still you need it for security purposes.
- ![image](https://github.com/user-attachments/assets/9d28e96c-ea8e-4d5f-8aaa-f14402f9851d)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/440029f0-3076-46c3-a1fe-c31aa9166abf)
- Last to delete is SNS. Head over to SNS, select Topics, then select your topic (don't click the name), then click "Delete" in the upper right hand corner.
- ![image](https://github.com/user-attachments/assets/381922c5-15af-49ce-b7b6-af240f6d737c)
- To confirm deletion, it asks you to type the phrase `delete me`. Then click "Delete".
- ![image](https://github.com/user-attachments/assets/962eabcc-5e2d-4369-b59c-f1818d9fe2f1)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/c5693854-ed10-44b9-8ee6-e655f802ffc9)
- Now that the Topic is deleted, let's make sure to delete the Subscription as well. Click it in the left hand navigation pane. Select it, and click "Delete".
- ![image](https://github.com/user-attachments/assets/9cdfc562-72b5-47fb-ad5d-9c10339531d8)
- Confirm the deletion by clicking "Delete".
- ![image](https://github.com/user-attachments/assets/682a3ddf-2971-4cec-885b-4abf8a37d0be)
- Confirmed!
- ![image](https://github.com/user-attachments/assets/56398cb1-dc82-498e-8435-ab2d52bdbf03)
- That completes our project!

Today, we accomplished a significant feat: building a robust security monitoring system in AWS! We leveraged several key services to achieve this, and I want to highlight the core learnings from each:

**1. AWS Secrets Manager:** We started by securely storing a sensitive "secret" in Secrets Manager. This is important for protecting sensitive data like API keys and database credentials, preventing them from being stored in plain text.

**2. CloudTrail:** We then configured CloudTrail to act as the "eyes" of our AWS account. It records detailed logs of all user activity, including attempts to access secrets in Secrets Manager. This provides a comprehensive audit trail.

**3. CloudWatch Logs & S3:** Next, we integrated CloudTrail with CloudWatch Logs. This allows us to store them within an **S3** bucket and analyze the CloudTrail logs for an extended period, going beyond the 90-day limit of the event history. We also learned how to create metric filters to narrow down the logs and focus on specific events, like secret access.

**4. CloudWatch Alarms:** We used CloudWatch Alarms to set up automated alerts. We created an alarm that triggers when a specific metric, representing secret access attempts, exceeds a threshold.

**5. Amazon SNS:** Finally, we integrated the CloudWatch alarm with Amazon SNS. This allows us to send email notifications whenever the alarm is triggered. This provides real-time alerts about potential security issues, enabling quick response times.

**In short, we learned how to use these AWS services to:**

- **Securely store sensitive data.**
- **Track user activity and access attempts.**
- **Store and analyze logs for extended periods.**
- **Automate alerts based on security events.**
- **Notify administrators in real time about potential breaches.**

This project provides a solid foundation for understanding and implementing AWS security monitoring, a crucial skill for anyone working in cloud environments.

- ![image](https://github.com/user-attachments/assets/4b7690a1-3fbc-48e9-9eb0-c109fbcbc533)
- ![image](https://github.com/user-attachments/assets/017e5cb1-204d-478d-acbf-1a00f838ee50)

---
