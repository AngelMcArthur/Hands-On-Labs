# Linux Project
<b>Objective</b> - Install VirtualBox on Windows. Download, Install, &amp; Configure Ubuntu Linux. 

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h1>Install VirtualBox on Windows:</h1>

<h2>Download VirtualBox</h2>

- <b>1. [Go to virtualbox.org](https://virtualbox.org)</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/2b1d986f-055e-429a-b4d3-635467639100)

- <b>2. Click giant button [Download VirtualBox "insert version number here" (ex: 7.0)]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d33598db-349e-4e11-bba7-9fb2cda84d9f)

- <b>3. Click on [Windows hosts]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/cd7856fa-1f43-4a46-a037-f73e38fa8fd3)

- <b>4. Once the download is finished, wait … DO NOT OPEN THE FILE AFTER DOWNLOADING.. the next step to verify the integrity of the file to make sure it's the correct one.</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/348880e6-dde2-4980-b533-be0b71679aeb)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Verify VirtualBox Integrity (Optional but Highly Recommended)</h2>

- <b>1. Open where your files are downloaded in file explorer to see if the file is there. If yes, then proceed to next step.</b>

- <b>2. In the address bar type "powershell" and a terminal should pop up in the folder where you have your downloaded file.</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d492a04b-e913-45a8-9285-e7260e0e3ad1)

- <b>3. In the terminal type "Get-FileHash", then the path, which by default is - C:\Users\(your_user_name)\Downloads.</b>
  - <i>(Note) If you can't find this then right click on your address bar and select "Copy Address".</i>
- <b>	After that, type, or copy and paste the ENTIRE file name including the ".exe". (EX: \VirtualBox-6.1.30-148432-Win.exe).
- Then after that type "-A SHA256".</b>
  - <i>The entire code should look something like this:</i>
  - `Get-FileHash C:\Users\wayne\Downloads\VirtualBox-6.1.30-148432-Win.exe -A SHA256`
  - ![Screenshot 2023-12-19 180311](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/b5a781ef-7714-4c37-a175-89b7633d82a9)

- <b>4. Press Enter and a Hash should pop up</b>
  - ![Screenshot 2023-12-19 180633](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/7153c0ea-482d-4ea6-a2da-fcae20f78f25)

- <b>5. Back on the downloads page of VirtualBox navigate to [SHA256 checksums] and click it. Leave the screen there until the next step.</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/36fc57c5-bc7c-4c18-82f5-ba7411cc80f7)

- <b>6. Place that screen with the SHA256 checksums and the PowerShell terminal side by side so you can compare the two.</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/293f485d-15f7-439a-8dca-0ed6e094b83d)

- <b>7. On the right hand side of the checksums, find the version of VirtualBox you have downloaded.
	- Then look at the string of letters and numbers next to it.
  - If it matches with the Hash in your PowerShell, then you are finished with the verification. If not, then you may have a manipulated file and should not open it.

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Download Ubuntu</h2>

- <b>1. [Go to ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d84d4b30-4976-4347-8110-fdc25c3f3d55)

- <b>2. Scroll down and click on the button with the highest version. EX: [Download 23.10]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/604ec342-7702-45ef-b0c0-979dc95c1f2b)

- <b>3. Wait for download to complete and move on to next step.
  -  ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/4f247bd8-acf6-4806-bd0b-2e63fe91299a)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Install VirtualBox</h2>

<h3>Page Name - Welcome to the Oracle VM VirtualBox "insert version number here" Setup Wizard</h3>

- <b>1. Action - Click [Next]</b>
  - <i>(Note) Click next to proceed with the setup. Otherwise, click cancel.</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/dd37ef3c-528e-468b-9baf-8439d88741b7)

<h3>Page Name - Custom Setup</h3>

- <b>2. Action - Click [Next]</b>
  - <i>(Note) Install all features of VB by just clicking next. Leave the "Location" alone unless you have no space on the default drive.
  - Click "Browse" if there is no space on the default drive and find another with more space.</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/bfe1e3b4-8497-4eb4-b35e-fae16174b3ee)

<h3>Page Name - Warning: Network Interfaces</h3>

- <b>3. Action - Click [Yes]</b>
  - <i>(Note) Temporary disconnect from the network</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d7871992-5307-49f9-83df-197a4f215a87)

<h3>Page Name - Missing Dependencies Python Core / win32api</h3>

- <b>4. Action - Click [Yes]</b>
  - <i>(Note) Installs missing dependencies</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/5a34b345-ef4f-4bcf-a669-cdd9641d8ffe)

<h3>Page Name - Ready to Install</h3>

- <b>5. Action - Click [Install]</b>
  - <i>(Note) Proceed with installation</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/7a127b27-1a05-46ba-9df8-6a3f50f6b297)
 
<h3>Page Name - Oracle VM VirtualBox "insert version name here" installation is complete.</h3>

- <b>6. Action - Click [Finish]</b>
  - <i>(Note) Leave checkbox for "Start Oracle VM VirtualBox after installation" checked to start up VirtualBox after clicking "Finish"</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/1df231dd-62de-4b60-8e9e-906eda1ed3fa)
 
<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Download VirtualBox Extension Pack (Optional)</h2>

- <b>1. Head back to the VirtualBox downloads page [virtualbox.org/wiki/Downloads]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/8e8544e3-8949-4167-8f46-5ccf8a8a96db)
 
- <b>2. Locate the header that says "VirtualBox (version number) Oracle VM VirtualBox Extension Pack" and click [All supported platforms]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/7e69654d-b876-4c36-b3eb-064de4c26c97)
 
- <b>3. Once the download is finished, click the file in your browser to run the "Setup Wizard"</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/f42fb355-ec67-4c83-8134-3e08d5ec3e51)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Install VirtualBox Extension Pack (Optional)</h2>

- <b>1. Action - Click [Install]
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/32b25bbc-5dc0-482d-b3e8-887f93e01cc3)
 
- <b>2. Action - Scroll down and Click [I Agree]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/e8598fe3-6a82-452b-b4d5-2bc808327dcf)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h2>Install Ubuntu</h2>

- <b>1. In VirtualBox click [New] in the Machine Tab</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/30a82427-f42f-4872-a21c-563a0c891847)
 
- <b>2. Create the VM
  - a) In "Name", type Ubuntu.
  - b) Leave "Folder" alone.
  - c) In "ISO Image" click the drop down menu and choose "Other…"
  - d) Find your Ubuntu ISO and click on it and then click [Open]</b>
  - ![Screenshot 2023-12-19 211718](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/78639fb6-7d36-4174-af33-58c6748214c1)

- <b>3. Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/fbd64944-c360-4213-9bc8-905ff9a6c2f8)

- <b>4. Unattended Guest Setup
  - a) Choose a username, hostname (device name) and password
  - b) Leave "Domain Name" alone
  - c) Click check box for "Guest Additions"
  - d) Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/4295bda6-3601-4aa4-a3b6-dfab7fef3680)
 
- <b>5. Allocate Memory and CPU
  - a) Give about 4,096 Megabytes (4.096 Gigabytes) and at least 4 CPUs
  - b) Enable EFI
  - c) Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/ddf9873e-8f41-4d2a-bc07-f8563deb74e6)
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/6d4d37a4-ee7d-4736-8370-eab1bb9f3245)
 
- <b>6. Choose Hard Disk Space and then click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d1a19a0d-7f31-4c79-bea5-54f934c109d4)

- <b>7. Review and Click [Finish]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/885d30fd-3b66-4c9e-9cbc-4790e8ce433f)

- <b>8. Wait for a few processes to run and then it should open up a new window and bring you to this screen (BIOS/EFI) then click [Enter] on "Try or Install Ubuntu":</b>
  - <i>(Note) If it gives you an error before the EFI screen and asks to mount a DVD again then just choose the same Ubuntu ISO and click next</i>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/cb069dac-0588-4112-8e50-9c3311deadb8)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->


<h1>Ubuntu Setup</h1>

<h2>Setup Pages</h2>

- <b>1. You should land on this screen. Choose your language and click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/25683594-becd-4fef-a7e9-43486f83be11)

- <b>2. Select Install Ubuntu and Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/4be39357-4a0c-43f1-81b5-22126c7d8c70)
 
- <b>3. Choose Keyboard Layout and Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/79af9ea6-542b-4527-a074-dbec0eb3ce5a)

- <b>4. Choose a network connection type and Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/b91360ae-93d2-49ad-a0a8-157955f0d41a)
 
- <b>5. Click the update if available</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/fb9d55fa-6683-4a7a-a271-4bffef7fd3ce)
 
- <b>6. Restart the Installer</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/29b52f49-0254-44e7-a7ca-e2fe2b219589)
 
- <b>7. Click [Next] until you reach this screen:
  - a) Default installation to reduce your attack surface
  - b) Install the drivers and support
  - c) Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/b72b7400-30a7-4def-a073-4933783138b8)

- <b>8. Choose manual partitioning and Click [Next]</b>
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d999fbea-aa16-4906-bde8-733c95f0d150)

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Define Multiple Partitions</h2>

<i>(Note) Will be using Ext4 instead of more secure/under development Btrfs for stability/simplicity but suggest learning how to set it up.</i>

<h3>1. Separate /boot</h3>

- <b>Select Free Space, then click the "+" sign.</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/050db9b3-d37b-4cb7-b251-c7c0ea149a77)
- <b>a) Size: 500 MB
- b) Used as: Ext4
- c) Mount Point: /boot
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/231587fe-451c-4d93-acce-651c54049631)
- <b>Result:</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/fbadacbe-e7e5-496b-a58c-68db6f772afc)
  - <i>(Note) /boot/efi is automatically created and that is normal.</i>

- <b>Notes:
- Priority - [LOW]
- Significance:</b> <i>Separating /boot helps in keeping the kernel and bootloader files isolated. This is useful in situations where the root file system is encrypted, as the bootloader can access the /boot partition without encryption.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->


<h3>2. Separate /swap</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: Amount of Ram you chose (4GB)
- b) Used as: Swap
- c) Mount Point: N/A
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/6a6d57a3-c24a-4a9b-b4e2-3ac5497052a3)
- <b>Result:</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/64ab8733-cb8e-428f-8e51-7c12faa3387c)

- <b>Notes:
- Priority - [LOW]
- Significance:</b> <i>Swap is a special area on the disk that the system uses as extra memory. A good rule of thumb is to make the swap partition the same size as your RAM, or 1.5 times that amount if you have less than 4GB of RAM.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>3. Seperate /home</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 5 GB
- b) Used as: Ext4
- c) Mount Point: /home
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/8e5c782f-1b0c-491c-a650-f54c814c2672)
 - <b>Result:</b>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/b90d3adf-7ab0-4ef8-8b57-7e473b1a28b4)

- <b>Notes:
- Priority - [LOW]
- Significance:</b> <i>Separating /home allows for easier system upgrades or reinstalls without losing user data. It also makes it possible to use different file system options, such as encryption, for /home.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>4. Separate /usr</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 5 GB
- b) Used as: Ext4
- c) Mount Point: /usr
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d63ca8a1-0d6e-4858-8b8c-b5778e1952b0)
- <b>Result:</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/3403dc64-db78-4ac8-bd26-d6b6ef9a4507)

- <b>Notes:
- Priority - [LOW]
- Significance:</b> <i>Separating /usr can be useful in scenarios where you want to mount it read-only or share it among multiple systems.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>5. Separate /var</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 2 GB
- b) Used as: Ext4
- c) Mount Point: /var
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/e1038f1d-c32e-42fe-8368-4f360c21b7d6)
- <b>Result:</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/0db258d5-a3f9-4a9d-834d-06e1badede03)

- <b>Notes:
- Priority - [MEDIUM]
- Significance:</b> <i>Separating /var is beneficial for systems with dynamic content such as databases and log files. It helps prevent issues related to running out of disk space in critical system directories.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>6. Separate /var/log</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 500 MB
- b) Used as: Ext4
- c) Mount Point: /var/log
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/8e149d1c-e728-4e69-9e5e-5d4fd651f95a)
 - <b>Result:</b>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/033dbfe5-7b16-453f-986a-557bce1ece1d)

- <b>Notes:
- Priority - [HIGH]
- Significance:</b> <i>Separating /var/log allows for better management of log files, preventing them from consuming all available disk space.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>7. Separate /var/log/audit</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 250 MB
- b) Used as: Ext4
- c) Mount Point: /var/log/audit
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/536fc19e-74a6-48db-80b5-a9cbc5656182)
 - <b>Result:</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/a33233df-4b2f-406b-b02f-249320c5a2c7)

- <b>Notes:
- Priority - [HIGH]
- Significance:</b> <i>Separating /var/log/audit allows for better management and isolation of audit logs.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>8. Separate /tmp</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 1 GB
- b) Used as: Ext4
- c) Mount Point: /tmp
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/10c4efc7-a120-4069-83ac-2811cc3239f9)
 - <b>Result:</b>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/339b9f39-00c1-429f-9b5e-8c159fe3abb4)

- <b>Notes:
- Priority - [HIGH]
- Significance:</b> <i>Separating /tmp allows for better management of temporary files.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>9. Separate /var/tmp</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: 5 GB
- b) Used as: Ext4
- c) Mount Point: /var/tmp
- d) Click [OK]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/4b41bf37-8433-473d-ac9e-35b3d88f6805)
 - <b>Result:</b>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/7aeac61a-f3f4-4d63-8ff6-dc2523c99849)

- <b>Notes:
- Priority - [HIGH]
- Significance:</b> <i>Separating /var/tmp allows for better management of temporary files.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>10. Separate /</h3>

- <b>Select Free Space, then click the "+" sign.
- a) Size: The Rest
- b) Used as: Ext4
- c) Mount Point: /
- d) Click [OK]
- e) Click [Next]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/856fc6fd-6cc7-4e31-8d42-75d86c22b47d)
 - <b>Result:</b>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/82f76a64-9510-4309-b044-db163a7fd0ce)

- <b>Notes:
- Priority - [HIGH]</b>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Ubuntu Finish Install</h2>

- <b>1. Click [Install]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/924e4733-beed-41a6-a9a9-a151503540ac)

- <b>2. Select Timezone then click [Next]</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/11a5c8a2-387b-487d-8205-7817b011216c)

- <b>3. Setup account by typing in your name, computer name, username, and password.
- 4. Then choose the "Require my password to log in" option for extra security.
- 5. Leave "Use Active Directory" unchecked.
- 6. Click [Next]</b>
	-	![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/8a84c31b-6ea8-4b73-946f-d3eda3e45085)
- <b>7. Choose your Theme</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/6d1055d3-2d65-4fa9-b7f6-a41f4c93fbfc)
- <b>8. Restart</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/0d1a4320-3c19-4392-8762-b8b7e98bad63)
- <b>9. Once it's booted back up then you can sign in.</b>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Harden VirtualBox</h2>

<h3>1. Enable Encryption</h3>

- <b>a) Click on VM (Ubuntu) and then click "Settings"</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/67e5eacd-decc-4c71-9145-43275988dc3a)
- <b>b) In the Settings popup, look in the "General" tab and within that tab click the "Disk Encryption tab.</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/072b3cb3-3f4a-492d-b573-11f01ba086fc)
- <b>c) Click the checkbox that says "Enable Disk Encryption"</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/01809a22-8c3c-426a-8aa7-ebcbb8929405)
- <b>d) Below that, where it says "Disk Encryption Cipher", select the drop down menu next to it and click "AES-XTS256-PLAIN64"</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/65dc1c85-99d1-4449-ad3b-e1f9f367b674)
- <b>e) Create a password  in "Enter New Password" and retype the password in "Confirm New Password"</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/60d490de-82b8-45a3-99b6-1ab4ccbafa29)
- <b>f) Click OK and wait for encryption</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/f29f6979-6236-466a-9d87-1435695483d0)

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>2. Set a Strong Password (Was complete during setup)</h3>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>3. Disable Unnecessary Features (All located in VM Settings)</h3>

- <b>a) USB Support:</b>
	- <i>(Note) If your virtual machine doesn't require USB devices, you can disable USB support to reduce the risk of potential attacks through USB connections.</i>
 		- In the left sidebar, select "USB."
   	- Uncheck the box for "Enable USB Controller."
   	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d63834c2-9ce6-4153-ad0b-755d4f7f2560)
 
- <b>b) Shared Folders/Clipboard:</b>
	- <i>(Shared Folder Note) If you don't need to share files between the host and the virtual machine, you can disable shared folders. This can help prevent unauthorized access to files.
		
	- (Clipboard Note) Disable clipboard sharing if you don't need to copy and paste data between the host and the virtual machine. This reduces the risk of sensitive information being inadvertently transferred.</i>
 		- Go to the "Shared Folders" tab.
   	- Remove any shared folders that you don't need.
   	- Alternatively, you can uncheck "Enable Shared Clipboard" under the "General" tab to disable all shared features.
  
- <b>c) Drag and Drop:</b>
	- <i>(Note) Similar to clipboard sharing, if you don't need to drag and drop files between the host and the virtual machine, you can disable this feature for added security.</i>
 		- Go to the "General" tab.
   	- Under "Advanced," set "Drag'n'Drop" to "Disabled" or "Host To Guest" if you need one-way drag and drop.
   	- ![Screenshot 2024-01-03 024908](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/ba627264-7edf-4022-814e-47dba52ea38d)

- <b>d) Audio Support:</b>
	- <i>Note) If your virtual machine doesn't require audio capabilities, you can disable audio support to reduce the attack surface.</i>
 		- Go to the "Audio" tab.
    - Uncheck the box for "Enable Audio."
    - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/a390008c-3965-4509-a460-e5aabc92a7a4)
 
- <b>e) Remote Display (VRDP):</b>
	- <i>(Note) If you don't need remote desktop access to your virtual machine, you can disable the VirtualBox Remote Desktop Extension (VRDE). This helps prevent potential remote access vulnerabilities.</i>
		- Go to the "Display" tab.
  	- Under "Screen," uncheck "Enable Server"
  	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/f0f821e8-5078-45dc-a012-c7f364d5913f)

- <b>f) Serial and Parallel Ports:</b>
	- <i>(Note) If your virtual machine doesn't need serial or parallel port support, you can disable these features to reduce the attack surface.</i>
 		- Go to the "Ports" tab.
   	- Disable any serial or parallel ports that you don't need.
   	- ![Screenshot 2024-01-03 033245](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/0fdbae0d-c84e-4ac5-97e6-0120f78a18e1)

- <b>g) 3D Acceleration:</b>
	- (Note) If your virtual machine doesn't require 3D graphics acceleration, you can disable this feature to reduce the risk of graphics-related vulnerabilities.
		- Go to the "Display" tab.
		- Uncheck the box for "Enable 3D Acceleration."
		- ![Screenshot 2024-01-03 033607](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/d44d3e82-dd92-4884-9be0-4fef92402a37)

- <b>Network Adapters:</b>
	- <i>(Note) Disable unnecessary network adapters if your virtual machine doesn't require multiple network connections. This helps reduce the potential for network-related attacks.</i>
 		- Go to the "Network" tab.
   	- Remove or disable any unnecessary network adapters. You can click on each adapter and click the checkbox to remove it.
   	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/2ac847c2-df38-4a04-b1a9-fa16435b88c4)

 <!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>4. Limit Resource Access (Completed when setting up amount of resources to allocate… adjust as needed)</h3>

 <!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>5. Monitor Virtual Machine Activity (All located in VM Settings)</h3>

- <b>a) Find and select the "System" tab.</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/5837c052-739c-46a2-95cd-024a2af262e3)
 
- <b>b) Enable Logs:</b>
	- Under the "Extended Features" section, check the box next to "Enable I/O APIC."
 	- Check the box next to "Hardware Clock in UTC Time."
  - <i>(Note) These settings enable additional logging features that can be useful for monitoring.</i>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/5580bb06-d355-44d0-9db6-bc226e00672a)

- <b>c) Click "OK" to save the changes and close the Settings window.</b>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>Viewing Virtual Machine Logs:</h3>

- <b>a) Start the Virtual Machine:</b>
	- Begin running the virtual machine as you normally would.
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/91251d6e-a47b-40f0-9506-94168f003add)
 
- <b>b) Access Logs:</b>
	- While the virtual machine is running, go to the "View" menu at the top of the VirtualBox window.
 	- Alternatively, when not running VM, at the top left menu, click on "Machine", then go to "Tools" and click "Logs".
  - ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/1f1a8157-a89e-4dfa-88aa-6e39d9e2cd6e)

<h3>Monitoring for Suspicious Activity:</h3>

- <b>c) Regularly Check Logs:</b>
	- <i>(Note) Periodically review the logs to check for any unusual or unexpected activities. Look for warnings or errors that might indicate issues or potential security concerns.</i>

- <b>d) Automate Log Monitoring (Optional):</b>
	- <i>(Note) You can set up external tools or scripts to automatically monitor the logs for specific events or patterns. This can be useful for real-time detection of potential security issues.</i>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Enable TPM & PAE/NX</h2>

<h3>a) Enable TPM</h3>

- <b>Click Settings and head to "System" tab.
- In the "Motherboard" section, click the drop down menu next to "TPM" and choose "v2.0"</b>
	- <i>(Note) TPM (Trusted Platform Module): Enabling TPM in VirtualBox enhances security by providing a dedicated hardware-based module that can securely store cryptographic keys, ensuring a higher level of protection against unauthorized access and tampering of sensitive data within virtual machines.</i>
 	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/62e5648c-f77f-4412-857b-59e2d0ac9fe4)

<h3>b) Enable PAE/NX</h3>

- <b>In the same area switch to the "Processor" tab instead of the Motherboard</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/c0a9302b-4edf-435e-a2f5-374a776d4eae)

- <b>Click "Enable PAE/NX"</b>
	- <i>(Note) Enabling PAE/NX in VirtualBox is beneficial for hardening as it allows the guest operating system to access more than 4 GB of physical memory, enhancing performance for memory-intensive tasks, and the No Execute feature helps mitigate certain security risks by preventing the execution of code in specific memory areas, adding an additional layer of protection against buffer overflow attacks.</i>
	- ![Screenshot 2024-01-03 235338](https://github.com/AngelMcArthur/Linux-Project/assets/55830075/747fe26e-c617-4402-aecb-54a780b2126b)

