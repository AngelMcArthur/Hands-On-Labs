
---

# [Azure DDoS Protection Hands-On Lab](https://www.youtube.com/watch?v=XaXqEMPO-KQ)

---

**Credits**

- This project was inspired by:
	- [A Guide To Cloud](https://www.youtube.com/@AGuideToCloud)

---

Azure DDoS Protection enables you to protect your Azure resources from distributed denial of service (DDoS) attacks with always-on monitoring and automatic network attack mitigation.

![image](https://github.com/user-attachments/assets/8c78ad08-ec5d-4ac3-838a-1cede096cb8f)
![image](https://github.com/user-attachments/assets/6211fade-7ea4-4958-a120-6f98712d036e)

**Supporting Vids**
-  [DDoS Attack Simulations](https://www.youtube.com/watch?v=xFJS7RnX-Sw) | Microsoft Security Community | Video

![image](https://github.com/user-attachments/assets/b069d0b9-5f61-4082-8905-7bba3c12c60e)

---

This project is a hands-on lab tutorial demonstrating Azure DDoS protection. I'll walk you through setting up DDoS protection for your Azure environment.

Here's a breakdown of the steps covered:

- **Creating Resources:** We'll begin by creating a resource group (`myResourceGroup`) and selecting the East US region.
- **Creating a DDoS Protection Plan:** A DDoS protection plan (`myDDoSProtectionPlan`) is created within the resource group, also in the East US region.
- **Creating and Configuring a Virtual Network:** A virtual network (`myVirtualNetwork`) is created. DDoS protection is enabled on this virtual network and linked to the previously created DDoS protection plan.
- **Configuring DDoS Telemetry:** A public IP address (`myPublicIPAddress`) is created with a static IP assignment. We then show how to configure DDoS telemetry using this public IP address. This step involves monitoring inbound packets dropped due to DDoS attacks.
- **Configuring Diagnostic Logs:** Diagnostic settings are created to collect detailed logs about DDoS protection, including mitigation flow logs and reports. These logs can be sent to different destinations.
- **Setting up DDoS Alerts:** A virtual machine (`myVirtualMachine`) is created and configured with the public IP address. Then, an alert rule is created to monitor for DDoS attacks on this public IP address.
- **Testing with a Simulated DDoS Attack:** Finally, we'll use a third-party tool (BreakingPoint Cloud) to simulate a DDoS attack against the public IP address to observe the effectiveness of the configured protection and alerts. We'll show the metrics confirming the simulated attack and the alerts triggering appropriately.

Remember that using BreakingPoint requires an account and may have usage limitations.

- **Key Skills/Tools:** Azure DDoS Protection, Virtual Network, diagnostic settings, alert rules, BreakingPoint Cloud

---

## **Task 1: Create a resource group**

- Log into your Azure Portal and click "Resource groups".
- ![image](https://github.com/user-attachments/assets/9143561c-8030-449f-98b6-9913886bac2a)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/7474be0b-6c67-4c41-9a80-f2ec0e9e7ccd)
- Name it `MyResourceGroup`. Then click "Review + create".
- ![image](https://github.com/user-attachments/assets/dc4bdad0-0bbd-47d8-87b1-f7400cbb9a47)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/a95457af-22d2-4259-85b0-95b691842945)
- Success!
- ![image](https://github.com/user-attachments/assets/d64fad3e-3916-4078-be4a-36d3fd2072db)

---

## **Task 2: Create a DDoS Protection plan**

- Search `DDoS protection plans` in the search bar and select the first Service.
- ![image](https://github.com/user-attachments/assets/809d61f2-ce20-4539-9f58-12dda4e45ccd)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/b68eade5-0cc4-4279-a3d4-5c5f831d454c)
- Select the resource group we just created `MyResourceGroup`. Name the instance `MyDDoSProtectionPlan`. Click "Review + create".
- ![image](https://github.com/user-attachments/assets/593bc169-3588-43ba-a009-a5c98c03b6ab)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/f57d87b6-5931-4b33-8d72-01a5f34ceb20)
- Success!
- ![image](https://github.com/user-attachments/assets/a06d8779-f1a3-4c40-99e7-60f310fcc357)

---

## **Task 3: Enable DDoS Protection on a new virtual network**

- Search `Virtual networks` in the search bar and click the first Service.
- ![image](https://github.com/user-attachments/assets/364dd5b6-268b-4584-b315-98708c49df20)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/670cea41-d9a2-40cf-93fd-0de592cdca26)
- Pick the same resource group and name the virtual network `MyVirtualNetwork`. Then click "Next".
- ![image](https://github.com/user-attachments/assets/9db1c5a0-3c81-426c-ac1e-e61d2524c5e8)
- In the Security tab, the only checkbox we check is "Azure DDoS Network Protection". Then choose the DDoS Protection plan we just created. Click "Review + create".
- ![image](https://github.com/user-attachments/assets/7ab64026-cd57-4344-841f-c0857cbd2fb8)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/8b23fda2-0b32-4080-8a5b-31543c5c5712)
- Success! Click "Go to resource".
- ![image](https://github.com/user-attachments/assets/3b23e3cb-bbca-442f-b77b-3eaf5012bcd0)
- In the left hand side bar, under "Settings", click "DDoS protection". Here you can see it is successfully enabled.
- ![image](https://github.com/user-attachments/assets/f64b8fc9-f94a-4065-91eb-140e1d750e83)

---

## **Task 4: Configure DDoS telemetry**

- Type "Public IP addresses" into the search bar and select the first Service.
- ![image](https://github.com/user-attachments/assets/fa28524c-b1d3-42c5-933e-68dee7cbe243)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/07da5dea-c691-4d5a-980a-c80602d31406)
- You'll be brought here:
- ![image](https://github.com/user-attachments/assets/0744e4ca-6105-4762-95fd-b051684bb3ee)
- Let's tackle this in sections (Change inputs accordingly):
- **Project details**
	- Subscription: `Azure subscription 1`
	- Resource group: `MyResourceGroup`
- ![image](https://github.com/user-attachments/assets/fa114017-4557-4db2-8531-dc84f8fd725a)
- **Instance details**
	- Region: `(US) East US`
- ![image](https://github.com/user-attachments/assets/aa7b83e5-5e79-4648-be9a-57fb944a78af)
- **Configuration details**
	- Name: `MyPublicIPAddress`
	- IP Version: `IPv4`
	- SKU: `Standard`
	- Availability zone: `Zone-redundant`
	- Tier: `Regional`
	- IP address assignment: `Static`
	- Routing preference: `Microsoft network`
	- Idle timeout (minutes): `4`
	- DNS name label: `mypublicdns321-am`
	- Domain name label scope (preview): `None`
- Click "Review + create".
- ![image](https://github.com/user-attachments/assets/2f9ffd47-6e49-446e-9a39-d00701caa9aa)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/bc5f1272-ea88-4b74-8d1a-dbb22612620a)
- Success! Click "Go to resource".
- ![image](https://github.com/user-attachments/assets/8347b925-91d7-4366-9de1-a79e88ce7b69)
- Once in the resource, click the "Settings" drop down menu in the left hand side bar, then click "Configuration". There we can see our IP address.
- ![image](https://github.com/user-attachments/assets/ef71fed1-8e82-440f-ad48-32f9c733081d)
- Now let's head back to our DDoS Protection Plan. When there, click the "Monitoring" drop down menu in the left hand side bar. Then click "Metrics". When there, click `MyDDosProtectionPlan` under "Scope".
- ![image](https://github.com/user-attachments/assets/8b9565dc-f986-47e6-be22-d574abdd8cc4)
- Click the checkbox for `MyPublicIPAddress` and click "Apply".
- ![image](https://github.com/user-attachments/assets/080a5064-1e7c-47ec-822b-d0c4844d0b14)
- Now under "Metrics", click "_Select metric_" and then select  `Inbound packets dropped DDoS`. Under "Aggregation", it will automatically select `Max` which is what we want.
- ![image](https://github.com/user-attachments/assets/e6a360fa-c618-40fe-9a43-da15aa7a00f0)
- Click "Save to dashboard" and then "Pin to dashboard".
- ![image](https://github.com/user-attachments/assets/d7016315-f2c4-4ec2-98f4-41ecc8929b03)
- Click "Create new", then name it `MyDashboard`, and click "Create and pin".
- ![image](https://github.com/user-attachments/assets/11e72d6b-656b-48ac-b5dd-b1d55e6038c8)
- Click on your notification to see your dashboard.
- ![image](https://github.com/user-attachments/assets/aebb3d5d-c54f-4a70-9f0d-b6add8c79d42)


---

## **Task 5: Configure DDoS diagnostic logs** - Optional!

> 💡 We are not going to use this for our project. so feel free to skip this section! However, in a more realistic scenario, we would want to have a place to send our logs for further analysis. We'll go over how to accomplish that now.

- Go back to the Public IP Address we created. Then in the left hand side bar, under the "Monitoring" drop down menu, click "Diagnostic settings". Here we will click "Add diagnostic setting".
- ![image](https://github.com/user-attachments/assets/2c229f93-76a8-44d5-9f23-512e7d7b2a6a)
- Once here, name it `MyDiagnosticSetting`. Select all of the "Categories" and "Metrics". For "Destination Details" you would choose the one that makes sense for your needs, with "Send to Log Analytics workspace" being a good choice for most if you have one set up already. I will click "Discard" here and move on with the project.
- ![image](https://github.com/user-attachments/assets/893ca3fb-1d5d-4a69-90cb-0dc13ca52013)

---

## **Task 6: Configure DDoS alerts**

### Create Virtual Machine

- First we need to create a Virtual Machine and within the VM, we will assign the Public IP Address we created. Search `Virtual machines` in the search bar and select the first option.
- ![image](https://github.com/user-attachments/assets/076a273f-643a-42c8-b85d-5f7224a37383)
- Click "Create" then select "Azure virtual machine".
- ![image](https://github.com/user-attachments/assets/2e9d05a1-a1b1-45f5-9450-1ddd65fa7e6c)
- You'll be brought to the "Create a virtual machine" page.
- ![image](https://github.com/user-attachments/assets/14d44adc-95ba-4c62-9042-30aff0598a7d)
- Let's go through this section by section:
	- **Project details**
		- Subscription: `Azure subscription 1`
		- Resource group: `MyResourceGroup`
	- ![image](https://github.com/user-attachments/assets/4c332379-7a9b-4949-b002-632906515cda)
	- **Instance details**
		- Virtual machine name: `MyVirtualMachine`
		- Region: `(US) East US`
		- Availability options: `No infrastructure redundancy required`
		- Security type: `Trusted launch virtual machines`
		- Image: `Ubuntu Server 24.04 LTS - x64 Gen2(free services eligible)`
		- VM architecture: `x64`
		- Run with Azure Spot discount: Leave unchecked
		- Size:
			- Click "See all sizes"
			- ![image](https://github.com/user-attachments/assets/5e3a4706-fe82-47a2-a143-d6b5e9020feb)
			- Type `B1` in the search bar. Then click the drop down menu for "B-Series". I will select `B1s(free services eligible)`. Then click "Select".
			- ![image](https://github.com/user-attachments/assets/1ea77d2d-7e23-41d8-94d5-794a4fce1477)
			- Done!
			- ![image](https://github.com/user-attachments/assets/98f645e7-ea3c-486c-9178-a0461cfa3cee)
		- Enable Hibernation: Leave unchecked
	- ![image](https://github.com/user-attachments/assets/e3478275-c978-4503-a4c1-5a911c5caee8)
	- **Administrator account**
		- Authentication type: `SSH public key`
		- Username: `azureuser`
		- SSH public key source: `Generate new key pair`
		- SSH Key Type: `RSA SSH Format`
		- Key pair name: `myvirtualmachine-ssh-key`
	- ![image](https://github.com/user-attachments/assets/a43db18b-a079-4207-9539-9c9379555be3)
	- **Inbound port rules**
		- Public inbound ports: `Allow selected ports`
		- Select inbound ports: `SSH (22)`
- Click "Review + create".
- ![image](https://github.com/user-attachments/assets/bf2c814a-cb60-4cea-9046-e8a8e0acb88e)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/998b7c60-29bb-4006-911a-4c7391fd7c26)
- Click "Download private key and create resource".
- ![image](https://github.com/user-attachments/assets/ba179629-20a9-4318-8926-b2f120153b4b)
- Success! Click "Go to resource".
- ![image](https://github.com/user-attachments/assets/ef152afd-96a6-436f-b54d-2cdb7c252f79)
- Once here, we need to assign the Public IP Address we created to this VM. To do that, we need to click on the "Networking" drop down menu, then click "Network settings". This is where we will click `myvirtualmachine620` (may be named something else for you) next to "Network interface".
- ![image](https://github.com/user-attachments/assets/4f25435f-66d4-4159-b2b0-e91f7d8ebab6)
- You'll be brought here:
- ![image](https://github.com/user-attachments/assets/afad96cb-149d-45c7-b2c0-eaf6e0363c07)
- Click the "Settings" drop down menu and within it, click "IP configurations". Click on `ipconfig1`.
- ![image](https://github.com/user-attachments/assets/d470bda7-9e21-4fb1-ae6c-1242b9e488db)
- You'll need to switch the "Public IP address" to `mypublicipaddress` from `myvirtualmachine-ip`. Then click "Save".
- ![image](https://github.com/user-attachments/assets/e9f5dec0-d3b8-4d92-8591-3fbc57a2a5ee)
- Success!
- ![image](https://github.com/user-attachments/assets/485f6b7b-172a-4593-a474-7e6b7b6a519a)


### Configure DDoS alerts

- Head to "Resource groups". Select `MyResourceGroup`.
- ![image](https://github.com/user-attachments/assets/434f1758-71c4-4d0e-8f49-e8f7492ecacd)
- Click `MyDDoSProtectionPlan`.
- ![image](https://github.com/user-attachments/assets/2a431eb0-9589-4e3f-b59a-eb01a43dcfab)
- On the left hand side, underneath "Monitoring", click "Alerts". Then click "Create alert rule".
- ![image](https://github.com/user-attachments/assets/c4a2ae6a-0e32-458f-8cf7-67f958d6fff4)
- In the "Scope" tab, click "Select scope". Check the checkbox for `MyPublicIPAddress` and then click "Apply".
- ![image](https://github.com/user-attachments/assets/933652eb-dede-4f16-a234-2795eaabfa89)
- Click "Next : Condition >".
- ![image](https://github.com/user-attachments/assets/fede0afb-1cc6-41c0-b274-f50c7b4ef554)
- Click "See all signals".
- ![image](https://github.com/user-attachments/assets/360775e6-f2b3-45e2-a0e5-a431418fb31a)
- Search `Under DDoS attack or not`, click it, and then click "Apply".
- ![image](https://github.com/user-attachments/assets/e0e33a32-97d5-43a1-ad8f-fadbedf0f421)
- After that, you will be brought to the configuration options for the signal. For "Threshold", put in `1`. Then click "Next : Actions >".
- ![image](https://github.com/user-attachments/assets/2eed1c95-db18-43f6-ba40-9c09feae4a78)
- For the "Actions" page, select `None`. Then click "Next : Details >".
- ![image](https://github.com/user-attachments/assets/70abb417-8dce-4113-9b39-96982cea6298)
- In the "Details" page, name this alert `My-DDoS-Alert`, then click "Review + create".
- ![image](https://github.com/user-attachments/assets/1204aee6-9269-4940-9ed0-ce968523db9b)
- Click "Create".
- ![image](https://github.com/user-attachments/assets/55fcc3b3-ffe6-4244-85ae-7f13c7cbb4c4)
- Success!
- ![image](https://github.com/user-attachments/assets/ad4c4a9c-f774-4b8a-a4bd-8b86d3381e0b)

---

## **Task 7: Submit a DDoS service request to run a DDoS attack**

- Now for the fun part! 😈 We need to run a DDoS attack! To do that we need to head to one of these services that are partnered with Microsoft:
- ![image](https://github.com/user-attachments/assets/53779d75-2249-4f68-b90e-f1bb3f42239d)
- We'll choose [BreakingPoint Cloud](https://breakingpoint.cloud/login) for this demonstration. Head to their website and sign up for an account if you don't have one.
- ![image](https://github.com/user-attachments/assets/9c9725dc-c780-4d52-a72e-a9e5fdb02247)
- Once you create an account you'll be greeted with this page where you will click "START TRIAL":
- ![image](https://github.com/user-attachments/assets/b447755e-8bae-4bca-9ad5-82de34499ff9)
- Before we start the simulation, head back to the dashboard you created with the metrics to see when the DDoS attack happens. If you don't know how to get back to it, then search `Dashboard hub` and click the first option.
- ![image](https://github.com/user-attachments/assets/78840618-fbd3-48fa-9f17-ca5f9bcbea21)
- Then click `MyDashboard`, not `My Dashboard`.
- ![image](https://github.com/user-attachments/assets/e497f978-8b5e-4339-8b84-5010b2506d08)
- You can also, redo it in the "Metrics" tab and watch from there.
- ![image](https://github.com/user-attachments/assets/16525e9d-7af6-4b5d-8dc3-188fc7352ac6)
- Head back to BreakingPoint and here is our screen we need to fill in:
- ![image](https://github.com/user-attachments/assets/1ce822d6-53fa-46ac-9297-5259a238b084)
- On the right hand side, click "Add Azure Subscription". You'll see a pop up to enter you Azure subscription ID.
- ![image](https://github.com/user-attachments/assets/6d3ab54c-0f60-4af1-9eca-8dbbaa9eb72f)
- To get that, head to your Public IP Address page (keep this tab open because we'll need it again) and copy it, then paste it in the field.
- ![image](https://github.com/user-attachments/assets/21468643-6cc6-4f1b-99ae-a21e6aa2e4ec)
- Paste it and then click "ADD SUBSCRIPTION".
- ![image](https://github.com/user-attachments/assets/99d6ed6b-c0b1-4933-aa11-fda1cf88c9d7)
- Click "Accept".
- ![image](https://github.com/user-attachments/assets/758a057b-a9c7-4b8f-a322-c871144a3413)
- Success! Click "Close".
- ![image](https://github.com/user-attachments/assets/e71b13ae-4655-458f-a9b1-5278de6d31be)
- For the Target IP Address, we can go to our Public IP Address page and copy it, then paste it in the field.
- ![image](https://github.com/user-attachments/assets/ea4959d2-ff18-4908-a196-6488dc196748)
- Change the "Port Number" to `443`, the "DDoS Profile" to `DNS Flood`, "Test Size" to `100K pps, 60 Mbps and 2 source IPs`, and the "Test Duration" to `10 Minutes`. Then click "START TEST"!
- ![image](https://github.com/user-attachments/assets/4cb79b1b-abe6-4ef4-b118-0ff8cc723e35)
- Here we go! The test is in progress! Now we wait for the 10 minutes to complete ⏲️
- ![image](https://github.com/user-attachments/assets/662e33cc-f45d-453c-8503-49416d570028)
- Halfway there! 🏁 When this is complete, we'll check back in Azure to see if it detected the attack.
- ![image](https://github.com/user-attachments/assets/e5caa56b-16bc-45d6-9416-e29f22442662)
- We're done! Let's see if anything was picked up in our metrics!
- ![image](https://github.com/user-attachments/assets/4ee594c9-3771-474e-a7ee-4782e2f846de)
- So for our "Alerts" we configured earlier, I'm not sure why it did not go off:
- ![image](https://github.com/user-attachments/assets/b488bc1a-2b95-4482-96b6-67ddb1bc2d3a)
- However, we DO have evidence that the DDoS attack DID in fact happen! Let's take a look at our metrics:
- ![image](https://github.com/user-attachments/assets/371ff054-28c2-4eea-9a03-2981b1070889)
- We can also change the chart type to see the spike a bit better!
- ![image](https://github.com/user-attachments/assets/b81a51b8-1365-44f3-9f53-830a3ade2490)
- Here is the Grid view as well:
- ![image](https://github.com/user-attachments/assets/4eb9987d-45fb-44fc-ba0c-7800fe47f99d)
- Play around with the metrics because this may be one of the only times you get to simulate a DDoS attack. It is helpful to study and see how it may look in a real environment. Don't be afraid to change up the filters as I have done here:
	- `Inbound packets DDoS` (Zoomed in to hours)
	- ![image](https://github.com/user-attachments/assets/8eb5e5ad-eb9c-489f-8564-7ff785fcaf0f)
	- `Inbound packets DDoS` (Zoomed in to minutes)
	- ![image](https://github.com/user-attachments/assets/f57226c9-7b28-4ffe-8921-6879dd4d4164)
	- `Inbound packets dropped DDoS` (Zoomed in to minutes)
	- ![image](https://github.com/user-attachments/assets/c39fce03-85a2-43a5-b430-04c6e830cd9d)
	- `Under DDos attack or not`
	- ![image](https://github.com/user-attachments/assets/e0ff9904-597b-4bf6-86ee-b120e1bf8248)
	- `Packet Count` (Zoomed in to hours)
	- ![image](https://github.com/user-attachments/assets/e8528e57-15ed-42a7-b8c1-3c8031a3f99d)
	- `Packet Count` (Zoomed in to minutes)
	- ![image](https://github.com/user-attachments/assets/8b01114a-eb71-4bc9-a140-f4649b43aa6e)

> That concludes our project! 🚨 Don't forget to delete your resources!

---

## Delete your resources 🗑️

- Go to your portal and head to "All resources".
- ![image](https://github.com/user-attachments/assets/7115c223-aac8-48fd-b48b-caf4bb588357)
- Select all resources and click "Delete". You may get some errors here, so I usually prefer to go into each of them one by one and delete it that way. It is tedious, but it makes sure that there are no leftovers increasing the bill.
- ![image](https://github.com/user-attachments/assets/4fab6e4b-9c93-4fad-911e-865f0c18e310)
- Type `delete` and then click "Delete".
- ![image](https://github.com/user-attachments/assets/e6cfce6b-0c0c-4130-ab6f-474dcd002fce)
- Success!
- ![image](https://github.com/user-attachments/assets/dde33498-88aa-4577-8432-d071e655f11e)
- Go to "Resource groups" and delete these as well.
- ![image](https://github.com/user-attachments/assets/67417195-8d4e-4f60-9849-01d7af895a59)
- Success!
- ![image](https://github.com/user-attachments/assets/d538413f-8a1d-4d19-a6ec-523fa6c67ee5)

---
