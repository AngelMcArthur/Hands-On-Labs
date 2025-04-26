# Networking Project
<b>Objective -</b> Build a SIEM with network monitoring using Wazuh &amp; Suricata.

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h1>Network Monitoring with Wazuh and Suricata</h1>
	
<h2>Install Suricata</h2>
	
- <b>1. Setup to install the latest stable Suricata:</b>
  - `sudo apt-get install software-properties-common`
  - `sudo add-apt-repository ppa:oisf/suricata-stable`
  - `sudo apt-get update`
	
- <b>2. Then, you can install the latest stable with:</b>
  - `sudo apt-get install suricata`
 
<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Configure Suricata</h2>
	
- <b>3. Then get the HOME_NET address from terminal paste:</b>
  - `ip a s`
	
- <b>4. Copy "inet"</b>
	
- <b>5. Open suricata configuration file</b>
  - `sudo code /etc/suricata/suricata.yaml`
  - <i>(Note) "code" can be replaced with your own editor like "vi" for vim or "nano" for nano. Code opens VS Code if you have it downloaded.</i>
	
- <b>6. Paste "inet into HOME_NET address</b>
	
- <b>7. Find "af-packet" and make sure of correct interface:</b>
  - <i>(Note) To do this, usually you can type "ctrl + w" and then type in "af-packet".</i>
	
- <b>8. Then look at interface and where it may say "eth0" by default, make sure it is correct by going into terminal and typing "ifconfig" and it should be the first word after that.</b>
  - <i>(Note) EX: enp0s3</i>
	
- <b>9. Scrolls down to pcap and paste in the same interface</b>
	
- <b>10. Find (ctrl + w) "community-id" and change value from "false" to "true"</b>
	
- <b>11. Save and quit the configuration file.</b>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Add extra rules to Suricata to detect threats</h2>

- <b>12. See what is available</b>
	- `sudo suricata-update list-sources`
	- <i>(Note) Some are paid</i>
		
- <b>13. To enable</b>
	- `sudo suricata-update enable-source et/open`
	- <i>(Note) Replace "et/open" with the ruleset you want.</i>
	
- <b>14. Then update the rulesets</b>
	- `sudo suricata-update`
	- <i>(Note) You may get warnings about protocols that weren't configured and set to enable. You will find those settings in the config file where you can enable or disable them yourself.</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Enable Automatic Rule updates</h2>
	
- <b>15. Type in terminal</b>
	- `sudo crontab -e`

- <b>16. Choose 1 to pick nano as the text editor</b>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/742560e8-4e5e-449a-90d6-ad9b7b944491)

- <b>17. Paste text into file to run update every 6 hours</b>
	- 0 0,6,12,18 * * * (/usr/bin/suricata-update && /usr/bin/suricatasc -c ruleset-reload-rules)
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/759406d6-d002-4c90-8cde-72bdda337a97)

- <b>18. Save and Quit</b>
	- "Ctrl + O" then "Enter" then "Ctrl + X"

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Enable Suricata</h2>
	
- <b>19. Type in terminal to enable suricata to start automatically after reboot</b>
	- `sudo systemctl enable suricata`
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/ecbcdec3-65f3-4129-a3d3-5f98f3dc0565)

- <b>20. To start Suricata now type</b>
	- `sudo systemctl start suricata`
		
- <b>21. Test if it is working by typing</b>
	- `sudo tail -f /var/log/suricata/fast.log`
		
	- <b>And while the terminal is open go to this website:</b>
	- testmynids.org/uid/index.html

	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/89b8bc21-7ef0-4ca6-9af6-9131d25e64c4)
	- <i>(Note) An alert similar to this should pop up in the terminal</i>

<!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Install logrotate</h2>
	
- <b>22. If logrotate is not installed on Ubuntu (usually installed by default), then type In terminal</b>
	- `sudo apt install logrotate`
	- <i>(Note) Logrotate rotates log files in Suricata. Log rotation avoids large files that can cause issues when opening. It does this by transferring log events to a new file without interrupting the logging process.</i>

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

<h1>Install Wazuh</h1>
	
<h2>Installation</h2>

- <b>23. Install curl</b>
	- `sudo apt install curl`

- <b>24. Go to wazuh.com/install/ and click "Quickstart"</b>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/2e64b4ad-4faa-457f-bf80-e8a18c7fc234)

- <b>25. Scroll down to "Installing Wazuh" and copy into terminal:</b>
	- `curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a`
	- <i>(Note) sometimes need to type -i to skip the check depending on your Linux distro.</i>
	- `curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i`
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/e3730baf-ac6a-4aec-a351-d9edf9a6089b)

- <b>26. Wait for it to finish installation and then COPY the User and Password information it gives to you.</b>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/fc9a5089-7149-40f1-8e84-a8d886bd01cf)

- </b>27. Allow https through the firewall
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/602e4670-a254-454f-92f5-b1b18ce014d2)
	- `sudo ufw allow 1515`
	- `sudo ufw allow 1514`
	- `sudo ufw allow 443`
	- `sudo ufw reload`

- <b>28. Access the Wazuh web interface with https://<wazuh-dashboard-ip> and your credentials:</b>
	- <i>(Note) Just type in your ip in your browser. For Example:</i>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/f4703be5-61d8-40dc-bb46-944d12b3313f)
		- Username: admin
		- Password: <ADMIN_PASSWORD>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/578c53c3-4636-451b-a03b-6c67e808edaa)
	- <i>	(Note) When you access the Wazuh dashboard for the first time, the browser shows a warning message stating that the certificate was not issued by a trusted authority. This is expected and the user has the option to accept the certificate as an exception or, alternatively, configure the system to use a certificate from a trusted authority.</i>

 <!-------------------------------------- SMALL BREAK -------------------------------------->

<h2>Configuration</h2>

- <b>29. Navigate to Wazuh Manager Server Configuration File</b>
	
- <b>30. On Wazuh drop down menu, click Management, then under "Administration" click "Configuration" and finally "Edit Configuration"</b>
	
- <b>31. Paste into around line 300 underneath the other "Log analysis" entries.</b>
	- `<localfile>
		  <log_format>json</log_format>
		  <location>/var/log/suricata/eve.json</location>
  	</localfile>`

	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/ca14b1da-46a9-48b5-b931-0d8059aba3bc)
	- <i>(Note) This allows the Wazuh agent to read the Suricata logs file.</i>

- <b>32. Click "Save" and "Restart Manager"</b>
	- ![image](https://github.com/AngelMcArthur/Networking-Project/assets/55830075/89940e80-271e-49f5-bf9b-9e63238c0e76)

 <!-------------------------------------- SMALL BREAK -------------------------------------->



