# IT-Project
<b>Objective</b> - Install Docker Desktop

<!---------------------------------------------------------------------- SECTION BREAK ---------------------------------------------------------------------->

While this is more in the scope of DevOps and SysAdmin, it all falls underneath the broad category of IT. Let's begin this quick demonstration

<h1>Install Docker Desktop</h1>

- <b>1. Create Docker Account</b>

<!----------------- BREAK ----------------->

- <b>2. Set up Docker's apt repository.</b>
  - <i>(Note) Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.</i>

  - Add Docker's official GPG key:
    - `sudo apt-get update`
    - `sudo apt-get install ca-certificates curl gnupg`
    - `sudo install -m 0755 -d /etc/apt/keyrings`
    - `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`
    - `sudo chmod a+r /etc/apt/keyrings/docker.gpg`
	
  - Add the repository to Apt sources:
	- `echo \
	   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
	   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
	   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`
	- `sudo apt-get update`

<!----------------- BREAK ----------------->


- <b>3. Install the Docker packages</b>
  - `sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

<!----------------- BREAK ----------------->
 
- <b>4. Verify that the Docker Engine installation is successful by running the hello-world image.</b>
  - `sudo docker run hello-world`
  - <i>(Note) This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.</i>

<!----------------- BREAK ----------------->

- <b>5. Download latest DEB package. https://docs.docker.com/desktop/install/ubuntu/</b>

<!----------------- BREAK ----------------->

- <b>6. Install the package with apt as follows:</b>
  - `cd Downloads/`
  - `sudo apt install ./docker-desktop-<version>-<arch>.deb`
  - <i>(Note) "<version>-<arch>" would be replaced by what is actually in your Downloads folder.</i>
  - ![image](https://github.com/AngelMcArthur/IT-Project/assets/55830075/f7b6500b-ffd5-4938-b1eb-e1a7ffa4b5d5)

<!----------------- BREAK ----------------->

- <b>7. Issues for some CPUs (Optional Troubleshooting)</b>
  - ![image](https://github.com/AngelMcArthur/IT-Project/assets/55830075/3146a952-11a8-4d5b-93af-13deccea0c36)
  - <i>(Note) When trying to open up Docker Desktop I was greeted with this error and after searching a bit I saw that it was because of this issue back when first setting up VirtualBox.</i>

  - <b>Navigate back to your VirtualBox Ubuntu VM "Settings", go to "System" and go to "Processor" and look back at the Extended Features section where we enabled PAE/NX back when hardening VirtualBox in the first part of the project.</b>
  - ![image](https://github.com/AngelMcArthur/IT-Project/assets/55830075/11ec0deb-41b5-41eb-9009-40f037192beb)

  - <i>(Note) "Enable Nested VT-x/AMD-V" is unable to be selected and greyed out. To fix this open cmd as administrator on your actual computer, which is presumably Windows, and then type</i>
  - `cd "C:\Program Files\Oracle\VirtualBox"`
  - `vboxmanage list vms`
  - `vboxmanage modifyvm "Ubuntu" --nested-hw-virt on`
  - <i>(Note) Ubuntu will be replaced by whatever you named your virtual machine. Keep the quotation marks around the name.</i>

  - Head back to VM Settings to see the new change
  - ![image](https://github.com/AngelMcArthur/IT-Project/assets/55830075/c4f7e665-09f0-4c52-a5b1-d3a1b75c5bb8)

<!----------------- BREAK ----------------->

- <b>8. Launch Docker Desktop</b>
  - To start Docker Desktop for Linux, search Docker Desktop on the Applications menu and open it. Alternatively, open a terminal and run:
  - `systemctl --user start docker-desktop`

- <b>9. Sign in</b>
  - <i>(Note) Docker Desktop relies on pass to store credentials in gpg2-encrypted files. Before signing in to Docker Hub from the Docker Dashboard or the Docker menu, you must initialize pass. Docker Desktop displays a warning if you've not initialized pass. You can initialize pass by using a gpg key. To generate a gpg key, run:</i>
  - gpg --generate-key
	
- <b>10. To initialize pass, run the following command using the public key generated from the previous command:</b>
  - pass init <your_generated_gpg-id_public_key>
  - <i>(Note) Once you initialize pass, you can sign in on the Docker Dashboard and pull your private images. When Docker CLI or Docker Desktop use credentials, a user prompt may pop up for the password you set during the gpg key generation.</i>

<h1>Hardened Docker Desktop</h1>

- <i>(Note) Hardened Docker Desktop is available to Docker Business customers only. https://docs.docker.com/desktop/hardened-desktop/</i>