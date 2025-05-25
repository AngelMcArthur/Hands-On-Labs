
---

# [Home SOC Honeypot with Azure Sentinel](https://www.youtube.com/watch?v=g5JL2RIbThM)

---

**Credits**

- This project was inspired by:
	- [Josh Madakor](https://www.youtube.com/@JoshMadakor)

---

In this guide, we set up a basic home SOC in Azure from scratch. Using a free Azure subscription, we walk through creating a virtual machine (VM), opening it to the internet as a honeypot, and forwarding logs to a central repository. We then integrate Microsoft Sentinel to analyze real-world attack data. We cover:

- Creating an Azure subscription and setting up a VM
- Configuring Log Analytics Workspace
- Forwarding logs and integrating with Sentinel
- Querying failed login attempts and visualizing attack sources
- Building an attack map to track real-time hacker activity

This project is great for cybersecurity beginners and professionals looking to practice log analysis, threat detection, and SOC operations in a real-world cloud environment.

- **Key Skills/Tools:** Azure VM, Log Analytics, Microsoft Sentinel, KQL, honeypot setup, attack mapping

- ![image](https://github.com/user-attachments/assets/d08a6f8f-41c9-4df0-b783-8c13be0ef6f5)
- ![image](https://github.com/user-attachments/assets/dce87db3-d912-4bc7-9dff-ab6cd5fa3e7f)

---

## **Step 1 - Create Free Azure Subscription**

- First, if you don't have one, create an Azure account by heading to their **[website](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account?icid=get-started)**. Then click "Try Azure for free".
- ![image](https://github.com/user-attachments/assets/b44308a5-2ba8-4abc-8ba8-787ed8177546)
- Create an account.
- ![image](https://github.com/user-attachments/assets/f6c46abe-a6e2-40e2-b2d6-4e4fce2ba7d0)
- Create a password.
- ![image](https://github.com/user-attachments/assets/b1600b02-860b-4371-a4c9-88946ed7aaca)
- Then continue through the process.
- ![image](https://github.com/user-attachments/assets/e493066f-ddf4-4dc1-a71a-8adcdc62a0ed)
- Click "Go to Azure portal".
- ![image](https://github.com/user-attachments/assets/9ca99aa8-4b8f-4d53-b040-cf30c097997b)
- Welcome to your Dashboard!
- ![image](https://github.com/user-attachments/assets/1830e3a2-e037-4051-bf63-9e0bb0c71304)

---

## **Step 2 - Create Virtual Machine**

- Here is what our current architecture looks like:
- ![image](https://github.com/user-attachments/assets/d37fdc4a-dc52-4315-b7c7-5b60eba2c018)
- It is blank with just our Azure subscription created. In this next section we will build a Resource Group, which can be thought of as a folder for our cloud resources (VM and Virtual Network).
- ![image](https://github.com/user-attachments/assets/894bea2f-48f6-409c-9869-54ee81fb75ad)
- Once we create the Resource Group, we will create a Virtual Network inside of the group.
- ![image](https://github.com/user-attachments/assets/a3113481-dad5-49bf-a154-d2f16a65250e)
- Then we'll create a Windows Virtual Machine and attach it to the Virtual Network. We'll then log into the VM and turn off the firewall to make it enticing to attackers on the internet.
- ![image](https://github.com/user-attachments/assets/cb6e0206-25f9-4bd1-ab0d-1843d2520325)
- We'll also create a Network Security Group. You can think of it as cloud firewall (Not to be confused with Azure Firewall which has more features). Once we create the NSG, we'll open it up completely along with the firewall inside of the Windows VM.
- ![image](https://github.com/user-attachments/assets/c3becda6-ef33-4417-af49-3d2893e6e3e7)
- When we're finished with this step, we will have a VNet with a VM inside of it that's wide open to the public internet and getting attacked.
- ![image](https://github.com/user-attachments/assets/430dd2a9-a0d5-492d-9588-dee377388735)

### 2.1 - Create Resource Group

- We'll get started by heading to **https://portal.azure.com/#home**.
- ![image](https://github.com/user-attachments/assets/51ff1801-3c4c-4b50-bff5-ae45be99223e)
- Search "Resource groups" in the search bar and then select it within 'Services"'.
- ![image](https://github.com/user-attachments/assets/6d7dcedb-e29c-4368-8df1-9bc4b8fbafb3)
- You'll be brought to the "Resource groups" page. Click "Create".
- ![image](https://github.com/user-attachments/assets/c1161647-6390-447f-b8c4-7c7f5d9c6c87)
- Now on the "Create a resource group" page, choose your **Subscription**, **Resource group name**, & **Region**. Then click "Review + create".
- ![image](https://github.com/user-attachments/assets/17daf4ce-8fc8-4963-bf51-b829b85c05fe)
- Make sure everything is correct, then click "Create".
- ![image](https://github.com/user-attachments/assets/4e5c825b-033f-4bde-ad63-bce023fb6783)
- Success! If you click within the resource group you'll see there is nothing inside of it at the moment.
- ![image](https://github.com/user-attachments/assets/4720158f-e31e-4238-9c9a-ccd77f17969b)
- Nothing to see here:
- ![image](https://github.com/user-attachments/assets/8277719f-2d9b-4a20-ab1a-8d3d37bb9be5)

### 2.2 - Create Virtual Network

- Let's create our Virtual Network to go inside the resource group. Search "Virtual networks" and select it in "Services".
- ![image](https://github.com/user-attachments/assets/53f7add9-8724-42c4-baf8-7260a9fb2646)
- Click "Create virtual network".
- ![image](https://github.com/user-attachments/assets/bceffa85-ea53-470e-87f6-2fa6eba018e9)
- You're brought to the "Create virtual network" page. Leave everything default and name your Virtual Network. Click "Next".
- ![image](https://github.com/user-attachments/assets/e9994707-e693-4388-a55e-92b2c856db12)
- Leave the "Security" section alone and click "Next".
- ![image](https://github.com/user-attachments/assets/8249738e-e9a3-456a-bf9d-7fc1f8774c90)
- Click "Next" on the "IP addresses" section to leave the IP ranges as default.
- ![image](https://github.com/user-attachments/assets/1f3c4653-7a9f-4b6b-bfcf-f8f0fff871f7)
- Skip the "Tags" section and click "Review + Create".
- ![image](https://github.com/user-attachments/assets/c671c85c-ffad-4bfe-bc3b-e8ced0e12d44)
- It may take a while.
- ![image](https://github.com/user-attachments/assets/88fa3719-2256-44fb-b911-4fa72ed91de3)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/37e9bf59-0ed9-4f17-a212-fe751f48e632)
- Success!
- ![image](https://github.com/user-attachments/assets/4a0cc536-185e-49bb-9f1a-ec814aaa5106)
- Head to "Resource groups".
- ![image](https://github.com/user-attachments/assets/6863aa64-ad10-486b-afaa-e3c0c7c84041)
- We can see `NetworkWatcherRG` has automatically been created. Click into the one we created so that we can see the VNet within.
- ![image](https://github.com/user-attachments/assets/3a10894c-1ecc-4441-9533-67a04db85f22)
- Here is our VNet we just created:
- ![image](https://github.com/user-attachments/assets/bd9fa63b-9eb0-44e7-8bef-31e566fa3ea6)

### 2.3 - Create Virtual Machine

- Now let's create a Virtual machine. Search "Virtual machines" in the search bar and click on the first option.
- ![image](https://github.com/user-attachments/assets/4d2cdd0a-dc53-41e7-a040-135aca74e9cd)
- Now it is time to create our honeypot. Click "Create" and then select "Azure virtual machine".
- ![image](https://github.com/user-attachments/assets/670df87f-59f2-479a-a9bc-8cc457c677ff)
- We'll be brought to the "Basics" section of "Create a virtual machine".
- ![image](https://github.com/user-attachments/assets/771c9a31-3c9b-4f76-aa63-99a2c638dc40)
- Let's tackle this piece by piece:
	- **Project details**
		- **Subscription:**
			- Default
		- **Resource Group:**
			- Select the one we created
	- ![image](https://github.com/user-attachments/assets/43e40997-acaa-4651-a290-4b326f949abe)
	- **Instance details**
		- **Virtual machine name:**
			- `CORP-NET-EAST-1`
		- **Region:**
			- Default
		- **Availability options:**
			- `Availability zone`
		- **Zone options:**
			- `Self-selected zone`
		- **Availability zone:**
			- `Zone 1`
		- **Security type:**
			- `Trusted launch virtual machines`
		- **Image:**
			- `Windows 10 Pro, version 22H2 - x64 Gen2(free services eligible)`
		- **VM architecture:**
			- `x64`
		- **Run with Azure Spot discount:**
			- Leave unchecked
		- ⚠️ **Size:**
			- If everything is greyed out, click "See all sizes". Then find the one closest to these specs:
				- **VM Size** - `Standard_D2s_v3`
				- **Type** - `General Purpose`
				- **vCPUs** - `2`
				- **RAM (GiB)** - `8`
				- **Data Disks** - `4`
				- **Max IOPS** - `3750`
				- **Local storage (GiB)** - `N/A`
				- **Premium disk** - `Supported`
				- **Cost/month** - `$70.08`
			- I found `D2as_v6`. Click "Select" when finished.
			- ![image](https://github.com/user-attachments/assets/6186b95a-5dd3-4bfd-a271-e1ec8461cd28)
		- **Enable Hibernation:**
			- Leave unchecked
	- ![image](https://github.com/user-attachments/assets/95665d60-a1b1-4ab9-976b-cbc600596155)
	- **Administrator account**
		- **Username:**
			- `labuser`
		- **Password:**
			- `******`
		- **Confirm password:**
			- `******`
	- ![image](https://github.com/user-attachments/assets/64ddfe4e-71fd-48e0-b3ea-1f5bb0621c26)
	- **Inbound port rules**
		- **Public inbound ports:**
			- `Allow selected ports`
		- **Select inbound ports:**
			- `RDP (3389)`
	- ![image](https://github.com/user-attachments/assets/e24cefb0-7808-492a-855e-83061df36237)
	- **Licensing**
		- Check the box for: "I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights."
		- Click "Next : Disks >".
	- ![image](https://github.com/user-attachments/assets/5b6e6336-dbd5-486b-bc63-5c26fa088f2b)
- Leave everything as default in the "Disks" section. Click "Next..".
- ![image](https://github.com/user-attachments/assets/f1386fee-4677-4f44-a055-509553382da1)
- Leave everything as default in the "Networking" section except for one option. Check the checkbox for "Delete public IP and NIC when VM is deleted". Click "Next..".
- ![image](https://github.com/user-attachments/assets/d16bb31c-90a6-4a2a-ba76-c9fcc6c80090)
- Leave everything as default in the "Management" section. Click "Next..".
- ![image](https://github.com/user-attachments/assets/64d1f664-0bd2-47ab-8739-38b1dc1191f8)
- Leave everything as default in the "Monitoring" section except for "Boot diagnostics" where we will choose "Disable". Click "Next..".
- ![image](https://github.com/user-attachments/assets/019af200-332b-4583-b471-bf15ae8ddabe)
- Click "Review + create" to skip the "Advanced" and "Tags" section.
- ![image](https://github.com/user-attachments/assets/42e77de9-5951-4154-a615-a971cc28c29c)
- Validation passed! Click "Create".
- ![image](https://github.com/user-attachments/assets/eb94c040-8d59-446e-bf96-f22da1b69a53)
- Success! The deployment is complete!
- ![image](https://github.com/user-attachments/assets/adbb605e-7fa9-4a4f-9c29-d0c39905750c)
- Head back to our resource group. We'll see our VM, Public IP address, NSG, NIC, Disk, and VNet.
- ![image](https://github.com/user-attachments/assets/67064102-ef61-4de2-b10f-6bd6f212b954)

### 2.4 - Open the NSG to the Internet

- We will now open up our NSG/firewall to the public internet. Click on your NSG. You'll be brought here:
- ![image](https://github.com/user-attachments/assets/ef4a1a7d-dd03-4aef-8d67-35b6fa4cb25a)
- The inbound rules control what traffic can enter the VNet from the public internet. If you see the "⚠️ **RDP** (Remote Desktop Protocol)" rule, it says RDP is enabled over the public internet for ANY person to connect from anywhere around the world to attempt to log in the VM. However, if you send any other type of traffic to it, there are no rules that will allow it, so it will get blocked. So currently, there's ONLY RDP allowed inbound. What we're going to do is delete the default RDP rule, and create another inbound rule that allows EVERYTHING inbound, not just only RDP. So the first action to take is to delete the RDP rule by clicking the trash can icon. Then click "Yes" at the pop up.
- ![image](https://github.com/user-attachments/assets/da96f2f5-06a5-4557-ada5-ba7ee40b6a1d)
- Success!
- ![image](https://github.com/user-attachments/assets/dfaa932a-0336-4533-b19f-2ab934b82c05)
- In the left hand side bar, click the "Settings" drop down menu, and then click "Inbound security rules".
- ![image](https://github.com/user-attachments/assets/f429faff-6b39-41b1-83c5-9dfa09f09e88)
- Click "+Add" in the top portion of the screen.
- ![image](https://github.com/user-attachments/assets/a8541d61-11a8-496b-b573-5ca44d7afee6)
- Choose these settings:
	- **Source**: `Any`
	- **Source port ranges**: `*`
	- **Destination**: `Any`
	- **Service**: `Custom`
	- **Protocol**: `Any`
	- **Action**: `Allow`
	- **Priority**: `100`
	- **Name**: `DANGER_AllowAnyCustomAnyInbound`
	- **Description**: Leave Blank
- Click "Add".
- ![image](https://github.com/user-attachments/assets/4441f2a9-9d71-4373-b9ca-a0ad9bf96cba)
- ![image](https://github.com/user-attachments/assets/bc360b04-4171-40c8-8576-6060c6b0c1c8)
- Success! The new rule is now added.
- ![image](https://github.com/user-attachments/assets/7b6742d0-7421-441e-85d7-5a54ad2b7630)

### 2.5 - Log into VM and Disable Windows Firewall

- Now we are going to log into our Windows VM and disable the firewall. Let's head to our Virtual machine. Click on your VM. Notice your Public IP address. Copy it for later use.
- ![image](https://github.com/user-attachments/assets/e5f95bbe-d7ad-4f09-9f8a-77a2424e0e4b)
- From here on we will be logging into our Windows VM with our Windows host computer using the Remote Desktop Connection app. If you use MacOS or Linux, then to follow along, find your way to log into the VM, or run Windows in a hypervisor such as VMWare or VirtualBox. Once you have the Remote Desktop Connection (RDC) app open, paste the IP address into where it says "Computer". Then press "Connect".
- ![image](https://github.com/user-attachments/assets/fa638730-99b9-4eee-a196-9f68e10b3c1f)
- Now enter your UN and PW we created earlier.
- ![image](https://github.com/user-attachments/assets/0c960ae8-4815-4754-9981-bee9c7ccb526)
- Click "Yes".
- ![image](https://github.com/user-attachments/assets/5d3a7284-3b75-48cb-ad5c-81a4e2ab6e27)
- The VM will now pop up on your screen. Click everything off and then click "Accept".
- ![image](https://github.com/user-attachments/assets/0546e356-db5e-4e35-b8a9-80f2b83fee27)
- Click "No" on the right hand side bar.
- ![image](https://github.com/user-attachments/assets/19ce18f5-6633-4449-a267-8f91e35900c6)
- Now that we are logged in, we need to head to the firewall settings. Type in `wf.msc` in the search bar. Click "Open".
- ![image](https://github.com/user-attachments/assets/bf31ad89-9d39-40c9-aaee-79b1e93eca5c)
- "Windows Defender Firewall with Advanced Security" should now pop up. In the bottom center, click "Windows Defender Firewall Properties".
- ![image](https://github.com/user-attachments/assets/c988245a-5c71-4444-b239-c583cdf04849)
- Now for each tab (**Domain**, **Private**, & **Public Profile**), click the "Firewall state" to be **off**. Then click "Apply" and "OK".
	- **Domain Profile**
	- ![image](https://github.com/user-attachments/assets/97dd9cf0-4359-43d2-bf93-c9b6bd37e40c)
	- **Private Profile**
	- ![image](https://github.com/user-attachments/assets/a5e44b79-2649-4155-bc06-68263f8a8963)
	- **Public Profile**
	- ![image](https://github.com/user-attachments/assets/57e52c4e-35a2-4878-b2b5-1407c1a126ef)
- You'll now see that Windows Firewall is now off.
- ![image](https://github.com/user-attachments/assets/0490fe34-8bc0-4057-a994-5ff18ec98299)
- Now we'll try and ping our VM from our local computer to make sure we can reach it over the public internet. If WE can, then that means HACKERS can! To ping from our own computer, we'll have to open a terminal.
- ![image](https://github.com/user-attachments/assets/dc31f041-c804-440d-ad53-db885228dd5a)
- Make sure to have our VM's Public IP address ready and run `ping -n 4 [VM.Public.IP.address]` (Without the brackets). This will send 4 pings to the VM, and if they are successful, the VM is reachable. Looks like the ones I sent have successfully gone through.
- ![image](https://github.com/user-attachments/assets/b62fa1a4-a449-42f4-b482-39ddb7837f39)
- Now we can move on to our next step! Here is a reminder of what our architecture looks like currently. It's only a matter of minutes or hours before bad bots start trying to log into our VM.
- ![image](https://github.com/user-attachments/assets/58b80d98-73fa-4f7e-98c4-713e1718e544)

---

## **Step 3 - Viewing Raw Logs on the Virtual Machine**

- We will now explore logging. First we will create an artificial log. Close your VM connection. Click "OK".
- ![image](https://github.com/user-attachments/assets/77572bad-50b1-4fa3-8a3c-ca9eed80f7cd)
- Now try to log back in, but with the wrong credentials. Do it 4 times for good measure.
- ![image](https://github.com/user-attachments/assets/7339607f-2e17-4d36-bcab-cc82a9289e6d)
- Now log in normally. We'll head to look into our local logs by searching "Event Viewer" and opening the app.
- ![image](https://github.com/user-attachments/assets/fe81209a-cbe1-4dd0-8603-2a80cc6a5e31)
- Here we can see the Windows Logs.
- ![image](https://github.com/user-attachments/assets/82a5cff1-4a3f-41ea-b45c-17fdf36eed28)
- To get a deep view of what we want, on the left hand sidebar, click the "Windows Logs" drop down menu. Then click "Security". Here we see immediately in "Keywords" column, our 4 failed attempts! It's labeled "Audit Failure" with an "Event ID" of "Logon".
- ![image](https://github.com/user-attachments/assets/a5eb86d9-c7b6-4dea-8cc0-500ffef35140)
- Double-clicking on one of the failures shows more information. Look into "Account Name" and it says `labuser` which was me trying to log in unsuccessfully. It also shows the "Source Network Address" (obfuscated), and "Workstation Name". You can also see a short description of the event in "Failure Information". Overall, not a bad amount of information uncovered by Windows Event Viewer. To gain more insight later in the project, we will forward these logs to Azure.
- ![image](https://github.com/user-attachments/assets/d2fd9439-2ee9-41b3-ac9a-08320253ed11)
- The logs in Event View can get a bit messy, so what we will do is filter down to just "Logon" events. In the right hand panel click "Filter Current Log..." and then type in `4625` in the Event ID field. Then click "OK". It will take some time to load.
- ![image](https://github.com/user-attachments/assets/1fe0a69e-16ce-4efe-9c87-0499ce0c10eb)
- Once it loads you will see every **Logon** attempt on this VM. Here we can see MANY log on attempts with information on who is trying to access and their IP address.
	- Account Name: `DESI`
	- ![image](https://github.com/user-attachments/assets/a8d15159-f8e0-4257-9e14-a8ccb801579a)
	- Account Name: `USER`
	- ![image](https://github.com/user-attachments/assets/8678bd1a-1c2d-4b3c-afeb-6a46c10c9da8)
	- Account Name: `CAROLINA`
	- ![image](https://github.com/user-attachments/assets/1f0adba4-9c96-4274-a8bf-22351dcf667f)
- All of this within seconds of each other! This really shows how essential a firewall is to a machine. That concludes this step. Let's now head back to Azure to create our log repository.

---

## **Step 4 - Creating Our Log Repository - Log Analytics Workspace**

### 4.1 - Create Log Analytics Workspace

- We're going to create a **Log Analytics Workspace**, which is Azure's log repository to store logs. Search "Log Analytics workspaces" in the Azure search bar. Select the first option.
- ![image](https://github.com/user-attachments/assets/69af3e28-df18-4aa1-83da-51e5a20b79f0)
- Click "Create log analytics workspace".
- ![image](https://github.com/user-attachments/assets/cdfcfdab-b70f-43ca-a74b-27b1016cab8b)
- Put it in our Resource group and name it whatever you want. Then click "Review + Create"
- ![image](https://github.com/user-attachments/assets/f94b7f4b-c8e6-42d2-9cfc-7f4dab6fd278)
- Validation passed! Now click "Create".
- ![image](https://github.com/user-attachments/assets/5729fc85-d096-4f05-b493-fac6792d29af)
- Success! Our Log Analytics Workspace (LAW) has been created.
- ![image](https://github.com/user-attachments/assets/88bb6547-8635-4eec-9beb-16606965f7a0)

### 4.2 - Create our Sentinel Instance - SIEM

- Microsoft Sentinel is Azure's SIEM (Security Information and Event Management). It will be used to organize our logs from the VM. Search "Microsoft Sentinel" in the search bar and select it from the results.
- ![image](https://github.com/user-attachments/assets/ccc17b05-b9a8-4685-88a2-881237cd89c1)
- Now click "Create Microsoft Sentinel".
- ![image](https://github.com/user-attachments/assets/f18026dc-9c18-4c99-8d44-ad7c80ac718c)
- Select our LAW and click "Add".
- ![image](https://github.com/user-attachments/assets/dbac8877-c301-47ab-9f08-9d5ce159610b)
- This action will link our **LAW** to our **SIEM**. When complete, we can access the logs that's in our log repository (LAW) from Microsoft Sentinel (SIEM). Keep in mind, there still isn't a connection from our VM and the LAW yet, so we will complete that later.
- ![image](https://github.com/user-attachments/assets/923dc9fb-758d-4d03-8d9f-9dbc34cf1a90)
- Success! It also let's us know our free trial has been activated, so we will have to keep that in mind in case we want to do future projects with Sentinel.
- ![image](https://github.com/user-attachments/assets/0c1b1062-b31c-4e28-9c7b-74f77a79fbe4)
- This completes this step. In step 5, we will configure the Azure monitoring agent security event connector. Basically, that will connect the VM and LAW.

---

## **Step 5 - Connecting our VM to Log Analytics Workspace**

- Here we will attempt the part of the architecture where we connect our VM to the LAW.
- ![image](https://github.com/user-attachments/assets/775c0bb9-e438-4ff6-b10b-04014852eb5c)
- Still in Sentinel, on the left hand side, click the "Content management" drop down menu, and then select "Content hub".
- ![image](https://github.com/user-attachments/assets/406f1a7e-aafb-458e-b98a-0d13ccbc603f)
- While here, in the search bar, search for "Windows Security Events". Then check the checkbox and click "Install" on the right hand side.
- ![image](https://github.com/user-attachments/assets/bfcfa4c3-0fbe-45a4-a63e-533e8776b6a3)
- Success! Now click "Manage".
- ![image](https://github.com/user-attachments/assets/bf10ef3a-dce7-4ca7-93bc-68268375d67c)
- You'll be brought to a screen similar to this one:
- ![image](https://github.com/user-attachments/assets/22fd18a2-b251-45e7-b20a-5ddb6c9ea586)
- Open up a new tab and navigate to your Virtual Machine. Click on it to enter, then, on the left hand side bar, click the "Settings" drop down menu. Then click "Extensions + applications" and leave it there. Notice it's empty in here. When you're done here, head back to the "Windows Security Events" tab.
- ![image](https://github.com/user-attachments/assets/6333ad81-f9bb-47a6-a089-86635fc84f7a)
- Back on the "Windows Security Events" page, check the checkbox for "Windows Security Events via AMA". Then click "Open connector page".
- ![image](https://github.com/user-attachments/assets/173fef9a-8389-492c-9b86-0b8e09664a59)
- Here we will click "Create data collection rule". This will get placed in our resource group. This rule is used by the VM to forward logs into our LAW, which lets us access them inside of our SIEM.
- ![image](https://github.com/user-attachments/assets/79870a06-366f-429e-8808-4975a464ae99)
- Name it anything you want, then click "Next..."
- ![image](https://github.com/user-attachments/assets/20edbdc9-0307-47f0-b2d6-ab8c1bf35109)
- Expand all the drop down menus and check the boxes. Then click "Next..."
- ![image](https://github.com/user-attachments/assets/fddcc3ab-1aad-492b-922c-8c6c4b8316d7)
- Leave default and click "Next..."
- ![image](https://github.com/user-attachments/assets/a9d1d11b-966d-410a-9cd3-841a540fd15e)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/60c0f685-ff79-4080-ae77-77b624781dbe)
- Success! Now let's head back to the other tab that shows the VM extensions.
- ![image](https://github.com/user-attachments/assets/81e4820d-adcd-4b87-8d5c-eae8041dbc37)
- Click "Refresh" and you can see `AzureMonitorWindowsAgent`. This agent will forward our logs.
- ![image](https://github.com/user-attachments/assets/8238fdef-6d44-48ff-a995-9b7302145711)
- Now open up one more tab and head to our LAW. Click into it, then on the left hand side, click "Logs". You'll see this "Queries hub" pop up and maybe another before it that you can exit out of to get to where we really need to be.
- ![image](https://github.com/user-attachments/assets/45618169-a93a-4daa-85ef-0ff54fc8ede6)
- Now we are here where we can analyze the logs. The only problem is where are the logs? If you look to the far right drop down menu, it says we are in "Simple mode". We'll change it to "KQL mode" and see what happens.
- ![image](https://github.com/user-attachments/assets/48e070c5-9ce5-4301-b81d-b4155ac6fd2d)
- Now we are in KQL mode. If you have heard of SQL, KQL should be very familiar. KQL, or Kusto Query Language, is a read-only query language used in Azure services like Azure Data Explorer and Microsoft Sentinel to retrieve, analyze, and visualize large volumes of structured and semi-structured data, such as our logs. It is designed to be simple and intuitive, allowing users to easily explore data, identify patterns, and perform real-time analytics. In the first line of the command, we have `SecurityEvent`. Let's see what happens when we click "Run".
- ![image](https://github.com/user-attachments/assets/411c39f9-5553-4e41-8b7b-625f4c2ff682)
- We've got data to sift through!
- ![image](https://github.com/user-attachments/assets/4fc7b2a2-2cf7-40eb-b7aa-387597061fd8)
- Let's learn how to navigate this in the next step!

---

## **Step 6 - Querying Our Log Repository with KQL**

- Here's a quick crash course in KQL! So `SecurityEvent` is what's known as a table. An easy way to think of a table is just a giant spreadsheet with various columns and rows that holds lots of information and logs. All of the logs that are coming in exist on the virtual machine and the Azure Monitoring Agent is forwarding into this central log repository called the Log Analytics Workspace (LAW). When we enter just the name of the table (`SecurityEvent`), it will dump the entirety of the table out without any filters.
- ![image](https://github.com/user-attachments/assets/20c24eb9-9a9d-4852-820d-d680c865f314)
- We don't always want to see every possible log, so we will use KQL to filter out certain events we want to see. For example, if we only want to see records where the "**Account**" is `domain\Administrator`, we'll run:

```SQL
SecurityEvent
| where Account == "domain\\Administrator"
```

> 💡 We add another `\` because it's a special character. The `|` and space before `where` is also essential for the query to be able to run without errors.

- ![image](https://github.com/user-attachments/assets/07dd1331-a591-4cfd-bc6e-687527466fd5)
- Let's open up the first log and see what is going on within. We've got the notorious Event ID `4625`, which indicates a **Logon** event. Look right below the Event ID and we can see the Activity which says `4625 - An account failed to log on.`
- ![image](https://github.com/user-attachments/assets/f019cfbe-3356-4ded-b8f1-1b660f538b13)
- Now you may notice by looking into these logs individually, that they tend to have a lot of extra and unwanted columns of information included. We can further filter this out by using `project`. Here's an example of improving on the command from earlier:

```SQL
SecurityEvent
| where Account == "domain\\Administrator"
| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
```

- ![image](https://github.com/user-attachments/assets/328304c1-37c0-41d0-a384-ea7bb67c46a7)
- We see that the clutter has slimmed down greatly to only the data we need for our analysis. Now we can see that this particular account is consistently trying to brute force their way into the VM, but failing to log on, which triggers Event ID `4625`. We also have their IP address, so let's see if we can use **Threat Intelligence** tools to find some information on them. Head to ipinfo.io and paste in the IP.
- ![image](https://github.com/user-attachments/assets/5c438daf-e680-43b4-a0b8-31028250d218)
- Seems they are in Saudi Arabia and the IP belongs to Oracle Cloud. Let's go back to Sentinel and adjust the code again so that we only see those columns, but remove the filter of where it's just that one account name.

```SQL
SecurityEvent
| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
```

- ![image](https://github.com/user-attachments/assets/062e93ce-68cf-40db-a443-79b66ebfa051)
- We have some more interesting finds. An account named `WORKGROUP\Administrator` with an IP address of `213.165.77.18` is also trying desperately to log on but to no avail. Let's filter it down again so that we can see only the failed log on attempts. We'll pull our trusty Event ID `4625` to show all failed attempts.

```SQL
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
```

- ![image](https://github.com/user-attachments/assets/a6a450b9-ae1b-48ec-ba80-78be433ec338)
- We can see it's most the same accounts going at it, but towards the bottom there was another contender named `\Test`.
- ![image](https://github.com/user-attachments/assets/2c68d6a5-7f53-46f9-a4db-58217c796ed0)
- We can also filter down further with time by using the following command to show only events from the last 5 minutes:

```SQL
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(5m)
| project TimeGenerated, Account, Computer, EventID, Activity, IpAddress
```

- ![image](https://github.com/user-attachments/assets/24ce36a0-259c-4df2-8e74-6142225a119f)
- That was fun! That completes this step. Here is another look at where we are in our architecture just as a reminder.
- ![image](https://github.com/user-attachments/assets/404cc7c2-098a-4a1e-9ccc-8129088896e8)

---

## **Step 7 - Uploading our Geolocation Data to the SIEM**

- In order to map out where the attacks are coming from, we need to upload our geolocation data to the Sentinel. In our current logs we are only bringing in the IP address, and we had to manually look up where one was attacking from. We're going to try and visualize it better using a map. First we are going to import a spreadsheet (as a “Sentinel Watchlist”) which contains geographic information for each block of IP addresses. **I'll attach the file [here]{}!** Once downloaded, head to "Microsoft Sentinel", click on your Sentinel instance, then, on the left hand side bar, click the "Configuration" drop down menu, and select "Watchlist".
- ![image](https://github.com/user-attachments/assets/38195c31-d052-4355-becb-866380c472d3)
- Click "New". It brings you to the "Watchlist wizard" page. In the "General" section, for "Name" and "Alias", enter `geoip`. Leave "Description" blank and click "Next..."
- ![image](https://github.com/user-attachments/assets/3e169d29-66de-413a-8b93-a1200e2263c9)
- On the "Source" section, drop the `geoip-summarized.csv` file in the "Upload file" area. You'll see it populate in the "File preview". For "SearchKey", choose `network`. Then click "Next : Review + create >".
- ![image](https://github.com/user-attachments/assets/abaed0f2-d50d-49d1-b542-368fabe25c68)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/6eda622c-11c2-4642-a576-41a534b5d0a3)
- It will take some time to upload. When complete, there should be `1` Watchlist and `55K` Watchlist Items.
- ![image](https://github.com/user-attachments/assets/d1c74f06-d222-4384-a8d2-9a056336f9b2)
- Success!
- ![image](https://github.com/user-attachments/assets/0ccf1829-203b-4fbf-a876-a826d1c0508f)
- That completes this step. Now we need to enrich our logs with the watchlist data!

---

## **Step 8 - Inspecting our Enriched Logs - We can see where the attackers are in the world**

- Heading back to our LAW, we can see that our watchlist is now a table that can be combined with the `SecurityEvent` table. Run `_GetWatchlist("geoip")` underneath `SecurityEvent`. Now we have enriched data! We have new columns such as **networks**, longitude, **latitude**, **countryname**, **cityname**, and **SearchKey**.
- ![image](https://github.com/user-attachments/assets/ad45b8bc-b1cc-40b8-95a8-9a32a50f23b7)
- In real life, this location data would come from a live source or it would be updated automatically on the back end by your service provider. Let's observe the logs now that we have geographic information, so we can see where the attacks are coming from. Run this:

```SQL
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```

- ![image](https://github.com/user-attachments/assets/6ac1e5ae-8873-4b5a-861c-632033c54036)
- This command just combined our command we ran a lot earlier, but now with the watchlist location data. Scroll all the way to the right and it says Los Angeles, California as the presumed location instead of Saudi Arabia for this user. Very peculiar results.
- ![image](https://github.com/user-attachments/assets/798d8af0-e2b1-4f70-8d68-369acf6c61dd)
- Run the above command with this and you can clean it up further by showing only useful information:

```SQL
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
| project TimeGenerated, Computer, AttackerIP = IpAddress, cityname, countryname, latitude, longitude
```

- ![image](https://github.com/user-attachments/assets/15c5062a-a59c-448d-9bf9-ffbab9d7a38b)
- With the logs now enriched, the last thing to do is create the map!

---

## **Step 9 - Creating our Attack Map**

- Head to Microsoft Sentinel, click on the Sentinel instance, click the "Threat management" drop down menu, and select "Workbooks".
- ![image](https://github.com/user-attachments/assets/4c38c65d-1582-464e-9b36-59e768a6fa57)
- Click "Add Workbook".
- ![image](https://github.com/user-attachments/assets/f2c26aaa-ec6a-499c-83a9-aa007e6c7362)
- Click "Edit".
- ![image](https://github.com/user-attachments/assets/6df45c38-5395-44da-98e5-c6d7b0e43e29)
- Remove all the elements by clicking the 3 horizontal dots and then selecting "Remove".
- ![image](https://github.com/user-attachments/assets/073e686f-585f-45c6-a9a2-3c9443e0c240)
- It should now be empty. Click "Add".
- ![image](https://github.com/user-attachments/assets/0c18ca8c-88c6-479e-807e-6c2179c75eb0)
- Click "Add query".
- ![image](https://github.com/user-attachments/assets/86632b8e-ae58-410b-9c18-a33c3405acff)
- You'll see this:
- ![image](https://github.com/user-attachments/assets/88ab6d76-7280-4e1e-ae08-83a4f1a83d04)
- Click "`</> Advanced Editor`".
- ![image](https://github.com/user-attachments/assets/f1c40253-558b-4676-b347-11971008b188)
- Erase everything and paste in this code:

```json
{
	"type": 3,
	"content": {
	"version": "KqlItem/1.0",
	"query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
	"size": 3,
	"timeContext": {
		"durationMs": 2592000000
	},
	"queryType": 0,
	"resourceType": "microsoft.operationalinsights/workspaces",
	"visualization": "map",
	"mapSettings": {
		"locInfo": "LatLong",
		"locInfoColumn": "countryname",
		"latitude": "latitude",
		"longitude": "longitude",
		"sizeSettings": "FailureCount",
		"sizeAggregation": "Sum",
		"opacity": 0.8,
		"labelSettings": "friendly_location",
		"legendMetric": "FailureCount",
		"legendAggregation": "Sum",
		"itemColorSettings": {
		"nodeColorField": "FailureCount",
		"colorAggregation": "Sum",
		"type": "heatmap",
		"heatmapPalette": "greenRed"
		}
	}
	},
	"name": "query - 0"
}
```

- ![image](https://github.com/user-attachments/assets/2b821250-1e63-4412-bb8e-27c825ab4bdd)
- Click "Done Editing". Now you're brought to the map!
- ![image](https://github.com/user-attachments/assets/3b310b53-4894-41ac-ad82-b27b31b5b9a0)
- Click the "Save As" floppy disk icon. Name it `Windows VM Attack Map`, then put it in our resource group. Click "Save As". Then click the Save icon again.
- ![image](https://github.com/user-attachments/assets/01fd875d-636a-4ed5-9d02-010a28b097ef)
- Click "Edit" again, and then click "Edit" again in the bottom right corner.
- ![image](https://github.com/user-attachments/assets/061edadf-9f19-4855-9e2a-f240ac7b6e26)
- Click "Map Settings".
- ![image](https://github.com/user-attachments/assets/fa582ad2-a613-4808-98b7-d8708f76a745)
- Here we can just see how everything is done and if you want to configure anything if you're interested.
- ![image](https://github.com/user-attachments/assets/fb223e62-2045-4338-8813-752789afa2dc)
- Here is the final map.
- v1
- ![image](https://github.com/user-attachments/assets/078acb88-208a-41a5-a556-093ff9a0e1eb)
- v2
- ![image](https://github.com/user-attachments/assets/0ad1b990-968c-48e5-be66-91116a056a50)
- Overall, this project was very fun and I learned a lot! I hope you learned a lot as well! Here is the architecture once again:
- ![image](https://github.com/user-attachments/assets/e6241e94-602a-447a-b88a-941761ad2fbb)

---

## **Step 10 - Beyond the lab - Creating Incidents**

- You can expand this lab by setting up incident response processes in Microsoft Sentinel. Here's how:
	1. **Configure Analytic Rules:** Create alerts in Sentinel that trigger based on specific events or conditions, such as a large number of failed login attempts from a single IP address or a suspicious network connection.
	2. **Automate Incident Creation:** Set up the alerts to automatically create incidents when they are triggered.
	3. **Work through Incidents:** Investigate the generated incidents, collect additional evidence, and take appropriate action, such as blocking malicious IP addresses or isolating the affected VM.
	4. **Integrate Threat Intelligence:** Enhance your SOC by integrating threat intelligence feeds to identify known malicious actors or patterns.
	5. **Develop Playbooks:** Create standardized procedures for handling common security events and automating incident response workflows.

- **Tools to Explore:**
	- **KQL Tutorials:** Learn more about KQL to effectively query and analyze your logs. Resources like KC7 Cyber are available.
	- **Microsoft Sentinel Documentation:** Familiarize yourself with the features and capabilities of Microsoft Sentinel, including alert creation, incident management, and threat intelligence integration.

- By expanding this lab and exploring these tools, you can gain valuable experience in threat detection, log analysis, and SOC operations, making you even better prepared for a career in cybersecurity.

> **🚨 DON'T FORGET TO DELETE ALL YOUR RESOURCES!**

- **Log Analytics Workspace** 🗑️
- ![image](https://github.com/user-attachments/assets/0a8f77ac-c611-4252-94e1-bb1f4bd60f83)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/8bbd9f5f-a749-4757-a9c1-d093e2534c9c)
- **Virtual Machine** 🗑️
- ![image](https://github.com/user-attachments/assets/663540ca-f237-482c-98e0-2dbb187514bd)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/26eb7d9a-2e58-413e-a4ed-df97da83fcec)
- **Network Security Group** 🗑️
- ![image](https://github.com/user-attachments/assets/b6862238-63da-4596-ab28-24e921c46214)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/2f1969a5-f8c5-42ad-a1c6-aed98c8974c1)
- **Azure Workbook** 🗑️
- ![image](https://github.com/user-attachments/assets/aa3ea2d2-c8a0-4deb-b2af-0398105acc13)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/9786a978-a140-46af-b3d5-dc810a26d8a7)
- **Data Collection Rule** 🗑️
- ![image](https://github.com/user-attachments/assets/ff95f1f3-7624-41e3-84a2-9419afa83288)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/3fb12fd8-5c0e-4d93-9dd2-5fb8595e3f77)
- **Network Watcher** 🗑️
- ![image](https://github.com/user-attachments/assets/63ba508b-c000-4505-987e-12ebef6b80bf)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/3aa2fd92-3c79-4878-9b50-e523d4c43f3c)
- **Virtual Network** 🗑️
- ![image](https://github.com/user-attachments/assets/acc04f3e-b5d8-4e0b-b802-30eab66c73a5)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/1a08e2dd-b009-4784-9462-9f15c3bd8261)
- **Resource Group** 🗑️
- ![image](https://github.com/user-attachments/assets/e2661a95-0e83-4608-8c98-96252ae7a087)
	- Success! ✅
	- ![image](https://github.com/user-attachments/assets/0d83e343-3af8-46fb-9562-e8a1ef8bf0a9)

---
