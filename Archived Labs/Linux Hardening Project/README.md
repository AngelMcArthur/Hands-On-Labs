# Linux-Hardening
<b>Objective</b> - Hardening Ubuntu Linux

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h1>Ubuntu Hardening</h1>

<h2>Update and Upgrade</h2>

- <b>1. Open a terminal and run the following commands:</b>
  - `sudo apt update && sudo apt upgrade -y`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Install Unattended-Upgrades</h2>

- <b>1. Install Unattended-Upgrades to automatically install security updates</b>
  - `sudo apt install unattended-upgrades`
  - `sudo dpkg-reconfigure -plow unattended-upgrades`
- ![image](https://github.com/AngelMcArthur/Linux-Hardening-Project/assets/55830075/a692a7ed-2fc1-4759-aa95-19f99743bf8a)

- <b>2. Press [Enter] for "Yes"</b>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Remove Bloatware/Unnecessary Services</h2>

<h3>Remove Bloatware (choosing default installation when setting up makes this simpler)</h3>

- <b>1. List all packages</b>
  - `apt list --installed`
		
- <b>2. Remove packages</b>
	- `sudo apt purge (place package names here separated by a space)`

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h3>Disable Unnecessary Services</h3>

- <b>1. Check Running Services</b>
  - `sudo service --status-all`
		
- <b>2. Disable Services</b>
  - `sudo systemctl disable <service_name>`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Configure Firewall (UFW)</h2>

- <i>(Note) UFW come preinstalled on most Linux distros.</i>
	
- <b>1. Install and enable the Uncomplicated Firewall (UFW):</b>
  - `sudo apt install ufw`
  - `sudo ufw enable`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Secure SSH</h2>

- <i>(Note) If SSH is not needed, consider disabling it. If needed:</i>
	
- <b>1. Type in terminal:</b>
  - `sudo nano /etc/ssh/sshd_config`
		
- <b>2. Scroll down to PermitRootLogin yes and switch to:</b>
  - `PermitRootLogin no`
			
- <b>3. Save and quit out of nano</b>
  - a) Save - Ctrl + O
  - b) Confirm - Enter
  - c) Exit - Ctrl + X
			
- <b>4. Restart for changes to take effect</b>
  - `sudo service ssh restart`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Set Up Fail2Ban</h2>
	
- <b>1. Install and configure Fail2Ban to protect against brute-force attacks:</b>
  - `sudo apt install fail2ban`
			
- <b>2. Check if it is running</b>
	- `systemctl status fail2ban.service`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Set up AppArmor</h2>
	
- <b>1. Install AppArmor:</b>
- <i>(Note) AppArmor enhances system security by confining programs to a limited set of resources. It is usually already installed on most distros.</i>
  - `sudo apt install apparmor apparmor-utils`
			
- <b>2. Check if running:</b>
  - `sudo apparmor_status | more`

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h2>Install Lynis and Perform Audit</h2>

- <b>1. Install Lynis security audits:</b>
  - `sudo apt install lynis`
			
- <b>2. Perform Scan</b>
  - `sudo lynis audit system`
			
- <b>3. Check log in "/var/log/lynis.log" or read the report that is put out in a readable format after the scan is done.</b>
  - `sudo cat /var/log/lynis.log`
			
- <b>4. Remedy issues</b>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h1>Pentesting</h1>

<h2>1. General Vulnerability Scanners:</h2>

- Tool: OpenVAS (also consider sysechk and OpenSCAP)
  - Description: OpenVAS is an open-source vulnerability scanner that performs comprehensive vulnerability assessments.
  - Installation: Install on Ubuntu in Step 5 for Docker

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>2. Network Vulnerabilities:</h2>

- Tool: Nmap
  - Description: Nmap is a powerful network scanning tool that can discover open ports, running services, and potential vulnerabilities.
  - Installation: Install on Ubuntu using
	-	`sudo apt install nmap`
		
- Run a few commands:
  - Scan a single target
  - `nmap [target]`
		
- Scan an entire subnet (Scan using CIDR notation)
  - `nmap [IP address/cdir]`
		
- Perform an aggressive scan
  - `nmap -A [target]`
		
- And more commands here https://www.stationx.net/nmap-cheat-sheet/ or https://www.geeksforgeeks.org/nmap-cheat-sheet/

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>3. Wireless Network Vulnerabilities:</h2>
	
- <b>Tool: Aircrack-ng</b>
	- <b>Description:</b> Aircrack-ng is a suite of wireless network security tools for assessing Wi-Fi network security.
	- <b>Installation:</b> Install on Ubuntu using
			- `sudo apt install aircrack-ng`
	- <i>(Note) To complete a wireless network audit, you need a wireless network interface controller whose driver supports raw monitoring mode and can sniff 802.11a, 802.11b, and 802.11g traffic. Typical Wi-Fi adapters (usually built-in with your computer) don't have the ability to monitor traffic from other networks. You can only use them to connect to a Wi-Fi access point.With an Aircrack compatible Wi-Fi adapter, you can enable the ‘monitor mode’ with which you can sniff traffic from networks you are not connected to. You can then use that captured data to crack the password of that network.If you have a wireless NIC that supports monitor mode and want a great guide on how to complete this audit, visit https://www.freecodecamp.org/news/wifi-hacking-securing-wifi-networks-with-aircrack-ng/ or https://thecybersecurityman.com/2018/05/02/cracking-wi-fi-passwords-with-aircrack-ng/</i>
		
- <b>Step 1: Enable Monitor Mode</b>
	- <i>(Note) As mentioned above, Aircrack-ng will only work on wireless NICs that support “monitor” mode. Monitor mode is one of the six modes that some wireless NICs support and they are reserved specifically for wireless networks. By default, wireless NICs are set to “managed” mode, but they can also be set to ad hoc, mesh, repeater, and master mode. Unlike Promiscuous Mode, you don’t have to associate (authenticate) with an AP or wireless network to capture traffic. When set to monitor mode, the wireless NIC ceases sending any information in order to fully dedicate itself to a passive “monitoring” of all the wireless traffic it can receive within range.</i>
	
- <b>To put your wireless NIC into monitor mode, type</b>
	- `airmon-ng start wlan0`
- <b>Then to prevent interference type</b>
	- `airmon-ng check kill`
	
- <b>Step 2: Airodump-ng</b>
	- <i>(Note) Airodump-ng is a packet sniffing tool that works very similar to Kismet. This tool will display a list of Access Points (APs) and any clients (stations) that are associated to them.</i>
	
- <b>To start airodump-ng, type</b>
	- `airodump-ng wlan0mon`
	- <i>(Note) Take note of your AP and record the BSSID and the channel it’s broadcasting on.</i>
	
- <b>Step 3: Target the AP</b>
	- Close the original airodump-ng window and type into a new terminal
	- `airodump-ng –bssid "Your:BS:SI:DH:ER:E!" -c 1 –write WPA2handshake wlan0mon`
	- <i>(Note) you need to type in the BSSID of your AP instead, and not the example one I put in quotes. You also need to write the channel your AP is on.</i>
	
- <b>Step 4: Deauth</b>
	- <i>(Note) The purpose of a deauth off the network is so that when you re-authenticate, airodump-ng will capture the WPA handshake.</i>
	
- <b>Type this command into a new terminal:</b>
	- `aireplay-ng –deauth 50 -a "Your:BS:SI:DH:ER:E!" wlan0mon`
	- <i>(Note) Aireplay-ng is a packet injector. This command will inject 50 deauthentication frames onto your network. Again, you need to type your target AP’s BSSID. Once the deauth has completed, you can close the terminal, but still keep the airodump-ng terminal open.</i>
	
- <b>Step 5: Wait for a Captured Handshake</b>
	- You’ll know you’ve caught the WPA handshake when the message, WPA handshake: [BSSID] appears in the airodump-ng terminal.
	
- <b>Step 6: Crack the Password</b>
	- Install seclists for a collection of lists of common passwords
	- `sudo apt install seclists -y or sudo snap install seclists -y`
	- <i>(Note) The file is around 1GB in size.</i>
	
- <b>Type this in a new terminal window:</b>
	- `aircrack-ng WPA2handshake-01.cap -w /usr/share/wordlists/seclists/Passwords/Leaked-Databases/rockyou.txt`
	- <i>(Note) This will start aircrack-ng. The amount of time you’ll have to wait for the password crack will depend on the complexity of the password. To defend against this type of attack, you should have a very complex and lengthy password.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>4. Password Auditing:</h2>
	
- <b>Tool: John the Ripper</b>
	- <b>Description:</b> John the Ripper is a fast password cracker that can detect weak passwords.
	- <b>Installation:</b> Install on Ubuntu using
	- `sudo apt install john`
		
- <b>Install a wordlist</b>
	- `sudo apt-get install wamerican-large -y`
		
- To run John against user passwords, merge the shadow and password files with this command
	- `sudo /usr/sbin/unshadow /etc/passwd /etc/shadow /tmp/crack.password.db`
		
- <b>Run John on the new file (will take time and can periodically check progress by pressing a random key)</b>
	- `john /tmp/crack.password.db`
		
- <b>When John finishes, it will dump the results into a log which can be viewed with the command</b>
	- `john --show /tmp/crack.password.db`

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>5. Network Protocol Analysis:</h2>
	
- <b>Tool: Wireshark</b>
	- <b>Description:</b> Wireshark is a network protocol analyzer that can capture and analyze the data traveling back and forth on a network.
	- <b>Installation:</b> Install on Ubuntu using
	- `sudo apt install wireshark`
 	- ![image](https://github.com/AngelMcArthur/Linux-Hardening-Project/assets/55830075/116911b2-99fd-49e4-a3b7-80d65424e54d)
 	- <i>(Note) During the installation process, this messages will pop up. Choose "No" and just run as sudo whenever running Wireshark</i>

- <b>Run with</b>
	- `sudo wireshark`
		
- <b>Click at top left corner on the Wireshark logo to start capturing packets</b>
	- ![image](https://github.com/AngelMcArthur/Linux-Hardening-Project/assets/55830075/486c637b-9560-4180-8c18-40d29fd88d1c)

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>6. IoT Device Vulnerabilities:</h2>
	
- <b>Tool: Shodan
	- Description:</b> Shodan is a search engine for Internet-connected devices, providing information about various devices on the Internet.
	- <b>Installation:</b> Access through the Shodan website.
	- <i>(Note) You need to have Python installed on your computer in order to use the Shodan CLI.</i>
		
- <b>Create an account on https://account.shodan.io/</b>
		
- <b>Get access to the free Shodan API key.
	- <i>(Note) This API key can be retrieved by navigating to the "My Account" section of the Shodan website, linked at the upper right of the homepage.</i>
		
- <b>Once you have Python configured then you can run the following command to install the Shodan CLI:</b>
	- `pip install -U --user shodan`
		
- <b>To confirm that it was properly installed you can run the command:</b>
	- `shodan`
	- <i>(Note) It should show you a list of possible sub-commands for the Shodan CLI.</i>
		
- <b>Initialize the Shodan CLI with your API key:</b>
	- `shodan init YOUR_API_KEY`
			
- <b>Show commands related to alerts</b>
	- `shodan alerts -h`
			
- <b>Create a network alert</b>
	- `shodan alert create "My home network" <YOUR IP>`
	- <i>(Note) Make note of the resulting alert ID, you need it for the next steps</i>
		
- <b>Configure and enable a trigger for the alert</b>
	- <i>(Note) If you want to see what kinds of triggers are available run 'shodan alert triggers'. You can add more than one trigger to your alert by providing a comma-separated list of triggers as an argument to the command.</i>
	- `shodan alert enable <YOUR alert ID> new_service`
			
- <b>Check the alert configuration</b>
	- `shodan alert info <YOUR alert ID>`
			
- <b>Analyzing the statistics behind our alerts</b>
	- `shodan alert stats`
			
- <b>GUI Alternative</b>
	- <i>(Note) If you don’t like using the CLI or simply want to use a graphical user interface (GUI) to create and manage your alerts, you can do the same as before by going to https://monitor.shodan.io/dashboard and clicking “Setup Network Monitoring”</i>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h1>Automated Hardening</h1>

Automated hardening tools and scripts can be very useful for applying security configurations to Linux systems, and they often follow security standards such as DISA STIG or CIS benchmarks. However, it's important to note that the effectiveness of these tools may vary, and it's always recommended to review and test their configurations in your specific environment.

Here are some free automated hardening tools for Linux systems, sorted from generally more strict to less strict:

<h2>1. CIS-CAT (CIS Compliance Assessment Tool):</h2>
- <b>Automation/Context:</b> The Center for Internet Security (CIS) provides a set of benchmarks for secure system configurations.
- CIS-CAT is a tool that automates the process of assessing systems against these benchmarks.
- <i></i>(Notes) CIS benchmarks are widely recognized and cover various operating systems. The tool provides scoring and recommendations for improving security.</i>

<h2>2. OpenSCAP:</h2>
- <b>Automation/Context:</b> OpenSCAP (Security Content Automation Protocol) provides a framework to create and maintain security policies, automate system compliance checking, and generate reports.
- <i></i>(Notes) It supports various standards, including those from DISA STIG and CIS. OpenSCAP allows you to scan and remediate vulnerabilities.</i>

<h2>3. Hardening Framework (STIG-4-Debian):</h2>
- <b>Automation/Context:</b> This framework, also known as STIG-4-Debian, automates the application of DISA STIG controls to Debian-based systems.
- <i>(Notes) It's specifically tailored for Debian-based distributions and aims to bring them in compliance with DISA STIG requirements.</i>

<h2>4. Linux Security Expert (LSE):</h2>
- <b>Automation/Context:</b> Linux Security Expert (LSE) is a script that automates security hardening for Linux servers. It includes options to secure various aspects of a system.
- <i>(Notes) LSE is more focused on simplicity and covers general security practices rather than adhering strictly to specific benchmarks.</i>

<h2>5. Bastille Linux:</h2>
- <b>Automation/Context:</b> Bastille Linux is a hardening and reporting program that performs automated system hardening.
- <i>(Notes) While it's been used historically, development has slowed down, and it may not cover the latest security practices. It's more suitable for older systems.</i>

<h2>6. SecCheck:</h2>
- <b>Automation/Context:</b> SecCheck is a security scanner and hardening script for Unix/Linux systems.
- <i>(Notes) It provides checks for common security misconfigurations and can be used to automate some hardening tasks.</i>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->
