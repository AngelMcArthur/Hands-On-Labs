
---


<br/>

# [Building Snort 3 From Source: A Modern IDS/IPS on Ubuntu 25.04](https://www.youtube.com/watch?v=j7Wapw3Gxvg)


<br/>

---

**Credits**

- This project was inspired by:
	- [MyDFIR](https://www.youtube.com/@MyDFIR)

---


<br/>

## Summary

In this project, we walk through the step-by-step process of building and installing **Snort 3** from source, empowering you to enhance the security of your network with a fully customized IDS/IPS solution.

- **Snort** is a powerful, open-source Intrusion Detection and Prevention System (IDS/IPS).
    - It analyzes network traffic in real time and is freely available for download.
    - Both pre-built and custom rule sets are supported.
    - While Snort 2 and 3 are similar, Snort 3 introduces significant architectural improvements and is better suited for modern production environments.

Whether you're a cybersecurity enthusiast, a professional, or simply exploring open-source security tools, this comprehensive guide is for you.

- **Key Skills/Tools:** Snort 3, Ubuntu, source compilation, rule management, VirtualBox

### **Prerequisites**

- Ubuntu 25.04+
- VirtualBox (if running in a lab environment)
- A second VM (e.g., **Rocky Linux**) on the same **NAT Network** as the Ubuntu VM


<br/>

---


<br/>

## ❓ **Why was this guide needed?**

- If you've ever tried to install Snort using `apt`, you may have noticed that it installs **Snort 2.9.20**, a significantly outdated version. Meanwhile, as of **May 10, 2025**, the **latest release is Snort 3.7.4.0**.
- ![image](https://github.com/user-attachments/assets/9c897649-90df-4135-be86-263c7885ec3d)
- I don't know about you, but I prefer to work with the most up-to-date and patched software available. It's unclear why Snort 3 isn't available via `apt`, but rather than settle for the legacy version, **I committed to using Snort 3**.
- Without a doubt, I underestimated the complexity of this project. I expected this process to take about an hour, but it turned into a week-long troubleshooting, debugging, documenting, fiasco! The **[YouTube video](https://www.youtube.com/watch?v=j7Wapw3Gxvg)** that inspired this guide (released December 14, 2023) looked MUCH simpler 😅. Despite searching everywhere, I couldn't find a guide that precisely matched what I wanted. So, I embarked on my journey into my very first source build.
- <img src="https://github.com/user-attachments/assets/7625a410-e5b5-4d54-9036-d57ad3f7059e" width="100%" height="100%">


<br/>

- One major challenge: The **official Snort documentation** lists the required and optional dependencies but **doesn't provide direct installation instructions, links, or grouped commands**. That made the process a bit... tedious 🙃. I'm also not familiar with these packages, so it took some time do some light research on each of them.
	- See here on their **[official docs](https://docs.snort.org/start/installation)**:
	- ![image](https://github.com/user-attachments/assets/df1c394a-6b68-4ab3-a952-8dbe817cc565)
	- Same for their **Github** where they point you to their Github saying "Information on where to download each of these _required_ packages can be found here: **[Snort 3 Github](https://github.com/snort3/snort3/blob/master/doc/user/tutorial.txt#L6)**."
	- Links are included for some, but no simple "copy and paste these commands" like I'm used to seeing in other repos:
	- ![image](https://github.com/user-attachments/assets/0a26c949-3f77-4821-a190-e0f742ba0260)
	- Optional dependencies:
	- ![image](https://github.com/user-attachments/assets/3dbb08e9-6a17-498d-bc18-e1d6f3effb16)
	- From the Github README:
	- ![image](https://github.com/user-attachments/assets/4eac2490-7d01-4e4a-a297-cffc4f02dc74)
- So, I organized all dependencies, both required and optional, into this guide. Whether you prefer a manual walkthrough or a one-shot install script, you'll find everything here updated for **2025**.


<br/>

### Checking the Snort Version in APT

- We can also do a quick check for ourselves to confirm the `apt` version of Snort is outdated. Open a terminal and install Snort with `sudo apt install snort -y`. 💡 *Tip: If you’re concerned about version conflicts or system clutter, take a snapshot of your VM before installing.*
- ![image](https://github.com/user-attachments/assets/f7da1bfc-4dd3-4b65-828f-0f47fd01882d)
- Then check the installed version with `snort -V`.
- ![image](https://github.com/user-attachments/assets/d7dc3cf8-16c2-44cf-9880-5ce60b0058c7)
- We can see it is outdated. Uninstall snort with `sudo apt remove snort -y`.
- ![image](https://github.com/user-attachments/assets/e93e8244-6965-4426-84fa-163a47988376)
- Then `sudo apt autoremove -y`.
- ![image](https://github.com/user-attachments/assets/122740f0-ab2b-4a99-9693-fd7012ceb9ee)


---


<br/>

## **Pre-lab Setup**

- Before we begin, we need to ensure the lab environment is properly configured. **If you're installing this on your personal machine, you may skip this step. It applies only to VirtualBox users.**


<br/>


### Download VirtualBox

- Download VirtualBox from their **[website](https://www.virtualbox.org/wiki/Downloads)**.
- ![image](https://github.com/user-attachments/assets/f67de15e-b41e-4230-bdce-6b439c6e52d8)
- Install VirtualBox by going through the setup Wizard. If you need help watch **[this video](https://www.youtube.com/watch?v=qeRoPSGqrn8)**.
- ![image](https://github.com/user-attachments/assets/7bcfccc1-1476-49fd-90f5-c6cd5bed5e9f)

### Install Ubuntu 25.04

- Download the Ubuntu ISO from their **[website](https://ubuntu.com/download/desktop)**.
- ![image](https://github.com/user-attachments/assets/781feb82-ca78-4034-8353-8e46569b5114)
- Install it in VirtualBox. If you need help, watch **[this video](https://www.youtube.com/watch?v=QSZqLgidz14)**.
- ![image](https://github.com/user-attachments/assets/f137a0e6-3cbc-41a7-bff9-9a188a049f20)

### Install Any other VM *(I chose Rocky Linux)*

- Download the Rock Linux ISO from their **[website](https://rockylinux.org/download)**.
- ![image](https://github.com/user-attachments/assets/e95800df-334c-4f05-8528-5905f12ad2b2)
- Install it in VirtualBox. If you need help, watch **[this video](https://www.youtube.com/watch?v=pXBBeDnHWhM)**.
- ![image](https://github.com/user-attachments/assets/ad79b308-3d1a-4c93-98e4-d2e8cce7f0fc)

### Create a NAT Network

- If you need help with this step, watch **[this video](https://www.youtube.com/watch?v=BjkmDZ3OEh0)**. With VirtualBox installed and our two virtual machines created, we now need to place them on the same virtual network so they can communicate. By default, VirtualBox assigns VMs to a NAT adapter.
- ![image](https://github.com/user-attachments/assets/4f8ce0d3-25c3-4506-8d7d-7c380755ab8d)
- However, for direct VM-to-VM communication, we will use a **NAT Network** instead.
- ![image](https://github.com/user-attachments/assets/162d8f8a-58ef-4142-a207-de108871a1ad)
- We'll use the default NAT Network named `NatNetwork`, leaving most settings as-is except for **disabling DHCP**.
- ![image](https://github.com/user-attachments/assets/6afd2b18-c81f-4ed2-a8b3-ae5f4e29f0ed)
- Then, we'll assign static IP addresses to each VM as follows:
	- **Rocky Linux:**
		- IPv4 Address: **`10.0.2.32`**
		- IPv6 Address: Disabled
		- Netmask: `255.255.255.0`
		- Gateway: `10.0.2.1`
		- DNS: `8.8.8.8, 8.8.4.4`
	- ![image](https://github.com/user-attachments/assets/6f20979b-e81c-408e-a027-d06d0498a56e)
	- **Ubuntu with Snort:**
		- IPv4 Address: **`10.0.2.33`**
		- IPv6 Address: Disabled
		- Netmask: `255.255.255.0`
		- Gateway: `10.0.2.1`
		- DNS: `8.8.8.8, 8.8.4.4`
	- ![image](https://github.com/user-attachments/assets/db6e508e-53b0-4107-81b4-6f1b499fd681)

### Create Snort Account - Get Oinkcode

- We also need to sign up and get our Oinkcode - a unique identifier that must be entered into our Snort instance that will automatically pull in Snort rules. Head to the **[Snort website](https://www.snort.org/users/sign_up)** and sign up.
- ![image](https://github.com/user-attachments/assets/d2587317-5bbb-41e9-a181-9d5737058ba9)
- Once you confirm your email, log into your account and select the "Oinkcode" tab. There you will be presented with your credentials. Keep this tab open for later.
- ![image](https://github.com/user-attachments/assets/0d7450c6-5098-4b40-a3bc-24b2045602b0)


---


<br/>

## **(Step 0 - Optional) - Skip Steps (1)-(2.3) by running this script**

<br/>

### Copy Script

- If you'd like to skip **Step 1: Installing Snort 3 on Ubuntu** through **Step 2.3: Disable LRO & GRO using a service**, I've prepared a script that automates the entire process. I've manually verified and debugged each step to ensure it runs smoothly.
- For those who prefer to build Snort 3 manually, feel free to skip ahead to Step 1 and proceed with the detailed instructions. Otherwise, continue below to use the automated script.


<br/>

- Before running it, take a moment to **review the script** and modify it if needed to suit your environment or preferences.

```bash
#!/usr/bin/env bash
set -euo pipefail

# -----------------------------
# 1. Install APT Packages
# -----------------------------
echo " 🛠️  Installing APT packages..."
sudo apt update && sudo apt install -y \
  build-essential cmake pkg-config bison flex autoconf autotools-dev libtool \
  libpcap-dev libdumbnet-dev libluajit-5.1-dev libpcre2-dev libssl-dev openssl zlib1g-dev libhwloc-dev hwloc fig2dev \
  uuid-dev libmnl-dev libnetfilter-queue-dev libsqlite3-dev git ethtool \
  cpputest libcmocka-dev libunwind-dev golang-go libjemalloc-dev \
  asciidoc asciidoctor dblatex source-highlight w3m \
  bzr \
  liblzma-dev
echo " ✅ APT packages installed."

# ➕ Explanation:
# 📦 Installed to: Standard system paths (`/usr/lib`, `/usr/include`, `/usr/bin`, etc.)
# 👀 Seen by: All compilation steps automatically — no need for custom paths
# 🔁 Dependencies for: LibDAQ, Snort, libml, gperftools, etc.
# ✅ Visibility: APT-managed packages are globally available

# -----------------------------
# 2. Prepare working directory
# -----------------------------
# Get original user even when run with sudo
USER_HOME=$(eval echo "~${SUDO_USER:-$USER}")
INSTALL_FOLDER="$USER_HOME/snort"
mkdir -p "$INSTALL_FOLDER"
cd "$INSTALL_FOLDER"
echo "📂 Using install folder: $INSTALL_FOLDER"

# ➕ Explanation:
# 📁 Working dir: Creates and uses ~/snort (always in the original user’s home)
# 🧩 Used for: Keeping all downloaded source builds here (Boost, gperftools, etc.)
# 🔁 Used by: BOOST_ROOT and to keep source tree clean
# ✅ Visibility: Local storage only — not globally visible

# -----------------------------
# 3. Boost C++ Libraries (headers only)
# -----------------------------
echo " 🛠️  Downloading Boost 1.88.0 headers..."

# 👇 Adding this line to define Boost path for future use
BOOST_ROOT="$INSTALL_FOLDER/boost_1_88_0"

# ✅ Boost will now be downloaded into the INSTALL_FOLDER
wget https://archives.boost.org/release/1.88.0/source/boost_1_88_0.tar.bz2
tar xjf boost_1_88_0.tar.bz2
cd "$INSTALL_FOLDER"
echo " ✅ Boost headers ready at $BOOST_ROOT"

# ➕ Explanation:
# 📦 Stored in: $INSTALL_FOLDER/boost_1_88_0 (header-only; not compiled)
# 👀 Seen by: libml, Snort (when you pass -DBOOST_ROOT in CMake)
# 🧭 BOOST_ROOT="$INSTALL_FOLDER/boost_1_88_0" tells CMake where headers are
# ✅ Visibility: Only visible when BOOST_ROOT is passed during compilation

# -----------------------------
# 4. gperftools (Performance profiler)
# -----------------------------
# Install pprof (optional but useful for viewing gperftools output)
go install github.com/google/pprof@latest
export PATH="$USER_HOME/go/bin:$PATH"

echo " 🛠️  Building gperftools..."
git clone https://github.com/gperftools/gperftools.git
cd gperftools
./autogen.sh && ./configure --prefix=/usr/local && make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ gperftools installed."

# ➕ Explanation:
# 📦 pprof (Go-based viewer) installed to: $USER_HOME/go/bin
# 📦 gperftools installed to: /usr/local/{bin,lib,include}
# 👀 Seen by: Snort automatically, since /usr/local/lib and /usr/local/include are default search paths
# 🧭 --prefix=/usr/local ensures system-wide visibility of gperftools
# ✅ Visibility: Snort and other tools can see gperftools without needing extra flags

# -----------------------------
# 5. FlatBuffers (Google serialization lib)
# -----------------------------
echo " 🛠️  Building FlatBuffers..."
git clone https://github.com/google/flatbuffers.git
cd flatbuffers
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local .
cmake --build build -j"$(nproc)" && sudo cmake --install build
cd "$INSTALL_FOLDER"
echo " ✅ FlatBuffers installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/bin, /usr/local/lib, /usr/local/include
# 👀 Seen by: Anything looking in system-wide paths (most software by default)
# ✅ Visibility: CMake handles install paths; nothing custom needed.

# -----------------------------
# 6. SafeClib (safec C11 Annex K)
# -----------------------------
echo " 🛠️  Building SafeClib (safec)..."
git clone https://github.com/rurban/safeclib.git
cd safeclib
autoreconf -vfi && ./configure --prefix=/usr/local
make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ SafeClib installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/lib/libsafec.a, /usr/local/include/safeclib.h
# 👀 Seen by: LibDAQ, Snort, etc., looking for secure C functions
# ✅ Visibility: Autotools will find it automatically in `/usr/local`

# -----------------------------
# 7. libml (Snort ML engine)
# -----------------------------
echo " 🛠️  Building libml..."
git clone https://github.com/snort3/libml.git
cd libml
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ libml installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/lib/libml.so, /usr/local/include/snort/ml/*
# 👀 Seen by: Snort 3 itself (optional ML support)
# 🔁 Depends on: Boost
# ✅ Visibility: BOOST_ROOT passed in to CMake ensures Boost is found

# -----------------------------
# 8. LibDAQ (Snort Packet I/O)
# -----------------------------
echo " 🛠️  Building LibDAQ..."
git clone https://github.com/snort3/libdaq.git
cd libdaq
./bootstrap && ./configure --prefix=/usr/local
make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ LibDAQ installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/lib/libdaq.so, /usr/local/include/daq/*
# 👀 Seen by: Snort (required for packet acquisition)
# ✅ Visibility: Autotools installs to system-wide path `/usr/local`

# -----------------------------
# 9. Colm (for Ragel)
# -----------------------------
echo " 🛠️  Installing Colm (required for Ragel 7.x)..."
wget https://www.colm.net/files/colm/colm-0.14.7.tar.gz
tar -xzf colm-0.14.7.tar.gz
cd colm-0.14.7
./configure --prefix=/usr/local && make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"

# Ensure dynamic linker can find Colm libraries
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/colm.conf
sudo ldconfig
cd "$INSTALL_FOLDER"
echo " ✅ Colm installed."

# ─── Make sure Colm & Ragel are in PATH ────────────────────────────
export PATH="/usr/local/bin:$PATH"

# ➕ Explanation:
# 📦 Installed to: /usr/local/{bin,lib,include}
# 👀 Seen by: Ragel (needed to compile Ragel 7.x+)
# 🧭 --prefix=/usr/local explicitly sets system-wide install path
# ✅ Visibility: Confirmed by running ldconfig after install

# -----------------------------
# 10. Ragel (Required for Hyperscan)
# -----------------------------
echo " 🛠️  Installing Ragel (required for Hyperscan)..."
wget https://www.colm.net/files/ragel/ragel-7.0.4.tar.gz
tar -xzf ragel-7.0.4.tar.gz
cd ragel-7.0.4
./configure --prefix=/usr/local --with-colm=/usr/local \
  && make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"

echo " 🛠️  Updating shared library cache..."
sudo ldconfig
echo " ✅ Shared libraries updated."
echo " ✅ Ragel installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/{bin,lib}
# 👀 Seen by: Hyperscan (required to generate FSM code during build)
# 🧭 Default install path is /usr/local unless overridden
# ✅ Visibility: Ragel uses Colm at build-time, no runtime linking needed

# -----------------------------
# 11. Hyperscan (High-speed regex)
# -----------------------------
echo " 🛠️  Building Hyperscan..."
git clone https://github.com/intel/hyperscan.git
cd hyperscan
mkdir build && cd build
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/usr/local \
  -DBOOST_ROOT="$BOOST_ROOT" \
  ..
# ─────────────────────────────────────────────────────────────────────────
make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ Hyperscan installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/{bin,lib}
# 👀 Seen by: Snort (used for fast pattern matching)
# 🧭 BOOST_ROOT="$BOOST_ROOT" passed to CMake so Hyperscan can find Boost headers
# ✅ Visibility: Ensures Hyperscan doesn’t error out due to missing Boost

# -----------------------------
# 12. PulledPork3 (Snort rule manager)
# -----------------------------
echo " 🛠️  Installing PulledPork3..."
git clone https://github.com/shirkdog/pulledpork3.git "$INSTALL_FOLDER/pulledpork3"
sudo mkdir -p /usr/local/bin/pulledpork3 /usr/local/etc/pulledpork3
sudo cp pulledpork3/pulledpork.py /usr/local/bin/pulledpork3/
sudo cp -r pulledpork3/lib/ /usr/local/bin/pulledpork3/
sudo chmod +x /usr/local/bin/pulledpork3/pulledpork.py
sudo cp pulledpork3/etc/pulledpork.conf /usr/local/etc/pulledpork3/
echo " ✅ PulledPork3 installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/bin/pulledpork3 (scripts) and /usr/local/etc/pulledpork3 (config)
# 👀 Seen by: You (CLI tool, not a library)
# 🧭 Manual file placement ensures availability in PATH and config path
# ✅ Visibility: Script is executable from anywhere via /usr/local/bin

# -----------------------------
# 13. Update shared libraries
# -----------------------------
echo " 🛠️  Updating shared library cache..."
sudo ldconfig
echo " ✅ Shared libraries updated."

# ➕ Explanation:
# 📦 Affects: All libs installed in /usr/local/lib
# 👀 Seen by: System-wide apps including Snort and build tools
# 🧭 ldconfig refreshes the runtime linker cache
# ✅ Visibility: Ensures no "library not found" errors at runtime

# -----------------------------
# 14. Snort 3 (latest release)
# -----------------------------
echo " 🛠️  Building Snort 3..."
git clone https://github.com/snort3/snort3.git
cd snort3
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DENABLE_JEMALLOC=ON -DBOOST_ROOT="$BOOST_ROOT"
make -j"$(nproc)" && sudo make install
cd "$INSTALL_FOLDER"
echo " ✅ Snort 3 installed."

# ➕ Explanation:
# 📦 Installed to: /usr/local/{bin,lib,etc}
# 👀 Seen by: libdaq, Hyperscan, libpcap, Boost, LuaJIT, etc.
# 🧭 BOOST_ROOT passed explicitly so Snort build finds Boost headers
# ✅ Visibility: All required components are in /usr/local

# -----------------------------
# 15. Disable LRO & GRO (inline IPS)
# -----------------------------
echo " 🛠️  Disabling LRO & GRO..."
# Automatically detect default network interface
IFACE=$(ip route | grep default | awk '{print $5}')
echo "Detected primary network interface: $IFACE"

# Disable GRO and LRO
echo "Disabling GRO and LRO on $IFACE..."
sudo ethtool -K "$IFACE" gro off lro off

# Confirm changes
echo -e "\n🛠️  Current offload settings:"
ethtool -k "$IFACE" | grep -E 'generic-receive-offload|large-receive-offload'
echo " ✅ LRO & GRO disabled."

# ➕ Explanation:
# 📦 Interface tuning (not a package)
# 👀 Seen by: Snort in inline IPS mode to prevent packet loss
# 🧭 Automatically detects primary NIC and disables LRO/GRO
# ✅ Visibility: Confirmed with ethtool query

# -----------------------------
# 16. Remove old system path assumption
# -----------------------------
echo " 🛠️  Removing old system path assumption..."
sudo ln -s /usr/local/bin/snort /usr/sbin/snort
echo " ✅ Old system path altered."

# ➕ Explanation:
# 📦 Creates symlink for compatibility with init scripts & legacy tools
# 👀 Seen by: Older scripts expecting snort in /usr/sbin
# 🧭 Links /usr/local/bin/snort -> /usr/sbin/snort
# ✅ Visibility: `which snort` will show both paths

# -----------------------------
# 17. Verify installation
# -----------------------------
echo " 🛠️  Verifying installation..."
snort -c /usr/local/etc/snort/snort.lua
snort -V
echo " ✅ Installation verified."

echo " ✅ All components built & installed successfully."

# ➕ Explanation:
# 📦 Sanity check (not a package)
# 👀 Seen by: You 🙂
# 🧭 Confirms Snort can load its config and shows version
# ✅ Visibility: No errors during these two commands means success
```


<br/>

### Paste Script

- Now that you have copied the script, head to your Ubuntu VM and open `Text Editor`.
- ![image](https://github.com/user-attachments/assets/1b508b4f-15ff-4a19-892a-79c9d22df867)
- Paste the script.
- ![image](https://github.com/user-attachments/assets/2419ea70-de1d-4f28-8a37-112adbb324fc)
- Save the script with `ctrl + s`. Save it as `Snort3Install.sh`.
- ![image](https://github.com/user-attachments/assets/30a005c1-50fa-4827-a3e3-ec9f6d0450ea)


<br/>

### Run Script

- Open a terminal and run `ls` to show your script in the current directory.
- ![image](https://github.com/user-attachments/assets/1ed8b790-e515-4309-af0e-5f4b210a59f9)
- Run `chmod +x Snort3Install.sh` to make the script executable.
- ![image](https://github.com/user-attachments/assets/60dac02f-7703-406e-8c51-469676eb4003)
- Then, run `sudo ./Snort3Install.sh | tee install-log.txt` to run the script and place the entire output into a new file we will create called `install-log.txt`. This is useful if something goes wrong with the script and we need to debug, or for referencing purposes. 💡 *Tip: Take a snapshot, or backup before you run the script.*
- ![image](https://github.com/user-attachments/assets/8c53eac3-91cc-4fad-ba46-e88a01aab273)
- Now we wait... You can view the contents by going to your `home` folder. You will see a `snort` folder.
- ![image](https://github.com/user-attachments/assets/bd304d3c-361b-4c00-a414-71f32c5d8f52)
- Click into `snort` and you can see the contents being created in real-time.
- ![image](https://github.com/user-attachments/assets/1834cdfe-c510-4cb8-88b4-98c79b0dec52)
- After around 20-30+ minutes, the script is complete! When your script is complete, scroll up to where it says ` 🛠️  Verifying installation...`. This is where we can see if it was installed correctly. Here is it validating the configuration:
- ![image](https://github.com/user-attachments/assets/9af78955-ef1d-4df0-814d-f30751005553)
- The rest of the output:
- ![image](https://github.com/user-attachments/assets/3786b317-6b52-4ae1-8a12-03e0c3f03727)
- Finally, here is where we confirm we are running the latest version of Snort, `Version 3.7.4.0`.
- ![image](https://github.com/user-attachments/assets/8483c1f2-09b8-4a56-b5c4-9e875d313d60)


<br/>

---

## **(Step 1) Installing Snort 3 on Ubuntu**


<br/>

### (1.1) Update Ubuntu

- The first step we need to take is to open your terminal and update Ubuntu using the command `sudo apt update && sudo apt full-upgrade -y`. Then enter your password. Wait for everything to finish updating.
- ![image](https://github.com/user-attachments/assets/b54a4d52-bed8-45ec-a15f-46027a97600f)
- There are a few ways to install Snort, however, we will be downloading it via Ubuntu. First, make a directory called `snort` using the command `mkdir snort`. Type `ls` or `ls | grep snort` to confirm the directory was created.
- ![image](https://github.com/user-attachments/assets/3d03bec3-faa6-416c-8c19-651c66215ed7)
- Now we need to **install prerequisites**.


<br/>

### (1.2) Install APT Prerequisites

- **Install Required apt Packages**: These development tools and libraries are needed to build Snort 3 and its components from source. They include compilers, build tools, compression libraries, network libraries, optional documentation utilities, and performance libraries.
```bash
sudo apt update && sudo apt install -y \
  build-essential cmake pkg-config bison flex autoconf autotools-dev libtool \
  libpcap-dev libdumbnet-dev libluajit-5.1-dev libpcre2-dev libssl-dev openssl zlib1g-dev libhwloc-dev hwloc fig2dev \
  uuid-dev libmnl-dev libnetfilter-queue-dev libsqlite3-dev git ethtool \
  cpputest libcmocka-dev libunwind-dev golang-go libjemalloc-dev \
  asciidoc asciidoctor dblatex source-highlight w3m \
  bzr \
  liblzma-dev
```

- **🧱 Required Packages**
	- **🛠 Build & Compilation**
	    - `build-essential` – Core compiler tools (GCC, G++, Make, etc.) for building Snort from source.
	    - `cmake` – Required to configure and build Snort.
	    - `pkg-config` – Helps locate library build flags.
	    - `bison`, `flex` – Needed to build dependencies like DAQ.
	    - `autoconf`, `autotools-dev`, `libtool` – Build tools often required when compiling Snort dependencies like `libdaq`.
	- **🔌 Core Library Dependencies**
	    - `libpcap-dev` – Enables packet capture support (used by DAQ and Snort).
	    - `libdumbnet-dev` – Network utilities for Snort (equivalent of `libdnet`).
	    - `libluajit-5.1-dev` – Provides Lua scripting support.
	    - `libpcre2-dev` – Regular expression engine used in Snort rules.
	    - `libssl-dev`, `openssl` – Required for SSL inspection and file signature verification.
	    - `zlib1g-dev` – Used for decompression tasks in packet processing.
	    - `libhwloc-dev`, `hwloc` – CPU/core affinity support for optimized performance.
	    - `fig2dev` - a set of tools for creating TeX documents with graphics which are portable, in the sense that they can be printed in a wide variety of environments.
	- **🔧 Utility & Runtime Libraries**
	    - `uuid-dev` – Enables generation of unique identifiers (UUIDs).
	    - `libmnl-dev`, `libnetfilter-queue-dev` – Used by `libdaq` for Netfilter Queue support.
	    - `libsqlite3-dev` – Required for config/rule storage and logging support.
	    - `git` – Needed to clone repositories from GitHub.
	    - `ethtool` – Used for managing NIC settings (e.g., disabling GRO/LRO).
- **🧩 Optional Packages**
	- **🧪 Testing & Debugging**
	    - `cpputest`, `libcmocka-dev` – Optional C/C++ unit testing libraries for `make check` and module testing.
	    - `libunwind-dev` – Enables Snort to generate backtraces on crash (used for debugging).
	    - `golang-go` – Needed to install Google’s `pprof` tool for profiling.
	    - `libjemalloc-dev` – Optional memory allocator for performance tuning.
	- **📚 Documentation & Formatting**
	    - `asciidoc`, `asciidoctor` – Converts documentation to HTML format.
	    - `dblatex` – Converts documentation to PDF (requires AsciiDoc).
	    - `source-highlight` – Syntax highlighting for development and documentation.
	    - `w3m` – Terminal-based browser, used to build plain-text manuals.
	- **🔁 Version Control & Legacy Support**
	    - `bzr` – Bazaar version control system; may be needed for older dependencies.
	- **🧩 Other Optional Features**
	    - `liblzma-dev` – Enables decompression of `.xz`/`.lzma` files (e.g., SWF, PDF analysis).


<br/>

---


<br/>

### (1.3) Install from Repositories

- **Manually Clone and Build**: These are components not available via apt or which require specific build flags. All of these commands assume you’re in `~/snort`.


<br/>

- **`Boost C++ Libraries - Download (but don’t install)`**
	- Boost is a powerful collection of portable C++ libraries. Snort uses some components (like Regex, Filesystem, or Thread) for performance and compatibility, but here you're **just downloading the source**, not building or installing.

```bash
wget https://archives.boost.org/release/1.88.0/source/boost_1_88_0.tar.bz2
tar xjf boost_1_88_0.tar.bz2
cd ~/snort
```

- 💡 **`Boost` Code Explanation**:
	- `wget https://archives.boost.org/release/1.88.0/source/boost_1_88_0.tar.bz2`
		- **Explanation:** Downloads the Boost 1.88.0 source code archive using `wget`.  
	- `tar xjf boost_1_88_0.tar.bz2`  
		- **Explanation:** Extracts the `.tar.bz2` file into a new `boost_1_88_0` directory.  
	- `cd ~/snort`  
		- **Explanation:** Returns to the main Snort project directory for the next installation steps.


<br/>

- **`pprof`**
	- **Go-based profiling visualization tool**. It's used **only for unit testing and profiling during development** of gperftools. Your actual **installation of gperftools will still work 100% fine without it**. This is **not a blocker** for Snort or production use.

```bash
go install github.com/google/pprof@latest
export PATH="$HOME/go/bin:$PATH"
```

- 💡 **`pprof` Code Explanation**:  
	- `go install github.com/google/pprof@latest`  
		- **Explanation:** Installs the latest version of Google's `pprof` tool into your Go binary path.  
	- `export PATH="$HOME/go/bin:$PATH"`  
		- **Explanation:** Temporarily adds the Go binary directory to your shell `PATH` so you can run `pprof` immediately.


<br/>

- **`gperftools (Performance profiler)`**
	- **Google Performance Tools** used to enhance memory allocation and profiling. Snort can optionally be compiled with `jemalloc` or `tcmalloc` for better memory management under high-load conditions. This step builds and installs `gperftools` from source.

```bash
git clone https://github.com/gperftools/gperftools.git
cd gperftools
./autogen.sh && ./configure --prefix=/usr/local && make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`gperftools` Code Explanation**:  
	- `git clone https://github.com/gperftools/gperftools.git`
		- **Explanation:** Clones the official gperftools GitHub repository to your system.
	- `cd gperftools`
		- **Explanation:** Navigates into the gperftools source directory.  
	- `./autogen.sh`
		- **Explanation:** Generates the `configure` script using autotools, preparing the build environment.
	- `./configure --prefix=/usr/local`
		- **Explanation:** Configures the build with `/usr/local` as the target install directory.
	- `make -j"$(nproc)"`
		- **Explanation:** Compiles the source code in parallel using all available CPU cores.
	- `sudo make install`
		- **Explanation:** Installs gperftools system-wide into `/usr/local`.
	- `cd ~/snort`
		- **Explanation:** Returns to the main Snort workspace directory.


<br/>

- **`flatbuffers (Google serialization library)`**
	- A **high-performance serialization library** developed by Google. Snort 3 optionally uses it for fast binary data handling in machine learning and other modules.

```bash
git clone https://github.com/google/flatbuffers.git
cd flatbuffers
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j $(nproc)
sudo cmake --install build
cd ~/snort
```

- 💡 **`flatbuffers` Code Explanation**:  
	- `git clone https://github.com/google/flatbuffers.git`  
		- **Explanation:** Clones the Flatbuffers source code from the official GitHub repo.  
	- `cd flatbuffers`  
		- **Explanation:** Enters the Flatbuffers source directory.  
	- `cmake -B build -DCMAKE_BUILD_TYPE=Release`  
		- **Explanation:** Configures a release-mode build using CMake into a new `build/` subdirectory.  
	- `cmake --build build -j $(nproc)`  
		- **Explanation:** Compiles Flatbuffers using all available cores in the previously defined `build/` folder.  
	- `sudo cmake --install build`  
		- **Explanation:** Installs the compiled libraries and headers to the system (usually `/usr/local`).  
	- `cd ~/snort`  
		- **Explanation:** Returns to your working Snort directory.


<br/>

- **`SafeC (Safe C Library)`**
	- Safe C Library implementation of safer string and memory handling functions (like `strcpy_s`). Snort uses this to avoid common memory corruption bugs in C code.

```bash
git clone https://github.com/rurban/safeclib.git
cd safeclib
autoreconf -vfi && ./configure --prefix=/usr/local
make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`SafeC` Code Explanation**:  
	- `git clone https://github.com/rurban/safeclib.git`  
		- **Explanation:** Downloads the source code for SafeC from GitHub.  
	- `cd safeclib`  
		- **Explanation:** Navigates into the `safeclib` directory.  
	- `autoreconf -vfi`  
		- **Explanation:** Rebuilds the `configure` script and related build files using autotools.  
	- `./configure --prefix=/usr/local`  
		- **Explanation:** Sets up the build configuration to install under `/usr/local`.  
	- `make -j"$(nproc)"`  
		- **Explanation:** Compiles the SafeC library using all available CPU threads.  
	- `sudo make install`  
		- **Explanation:** Installs SafeC to the system.  
	- `cd ~/snort`  
		- **Explanation:** Returns to the Snort project directory.


<br/>

- **`libml (Snort's ML engine)`**
	- Snort’s **machine learning plugin engine**, built to interface with various detection modules that require ML-based classification or scoring.

```bash
git clone https://github.com/snort3/libml.git
cd libml
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`libml` Code Explanation**:  
	- `git clone https://github.com/snort3/libml.git`  
		- **Explanation:** Clones the Snort team's machine learning engine source code.  
	- `cd libml`  
		- **Explanation:** Enters the `libml` directory.  
	- `mkdir build && cd build`  
		- **Explanation:** Creates and enters a separate build directory to keep things organized.  
	- `cmake -DCMAKE_INSTALL_PREFIX=/usr/local ..`  
		- **Explanation:** Configures the build system with install path `/usr/local`.  
	- `make -j"$(nproc)"`  
		- **Explanation:** Builds the ML engine in parallel.  
	- `sudo make install`  
		- **Explanation:** Installs the ML library system-wide.  
	- `cd ~/snort`  
		- **Explanation:** Returns to the main Snort working directory.


<br/>

- **`LibDAQ (Snort Packet IO engine)`**
	- **Snort’s packet acquisition library**. It abstracts packet I/O so Snort can work across different platforms (e.g., `pcap`, `afpacket`, `netmap`) without needing to change the Snort codebase.

```bash
git clone https://github.com/snort3/libdaq.git
cd libdaq
./bootstrap && ./configure --prefix=/usr/local
make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`LibDAQ` Code Explanation**:
	- `git clone https://github.com/snort3/libdaq.git`  
	    - **Explanation:** Downloads the official LibDAQ repository from Snort’s GitHub.
	- `cd libdaq`  
	    - **Explanation:** Changes directory into the newly cloned LibDAQ source folder.
	- `./bootstrap`  
	    - **Explanation:** Prepares the build environment using autotools (generates `configure` script).
	- `./configure --prefix=/usr/local`  
	    - **Explanation:** Configures the build to install LibDAQ into `/usr/local`.
	- `make -j"$(nproc)"`  
	    - **Explanation:** Compiles the source code using all available CPU cores.
	- `sudo make install`  
	    - **Explanation:** Installs the compiled library files system-wide.
	- `cd ~/snort`  
	    - **Explanation:** Returns to the main Snort working directory.


<br/>

- **`Colm`**
	- A **programming language and runtime** required by Ragel. It must be installed before building Ragel.

```bash
wget https://www.colm.net/files/colm/colm-0.14.7.tar.gz
tar -xzf colm-0.14.7.tar.gz
cd colm-0.14.7
./configure && make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`Colm` Code Explanation**:
	- `wget https://www.colm.net/files/colm/colm-0.14.7.tar.gz`  
	    - **Explanation:** Downloads the Colm source archive.
	- `tar -xzf colm-0.14.7.tar.gz`  
	    - **Explanation:** Extracts the contents of the tarball.
	- `cd colm-0.14.7`  
	    - **Explanation:** Moves into the extracted directory.
	- `./configure`  
	    - **Explanation:** Configures the build environment for your system.
	- `make -j"$(nproc)"`  
	    - **Explanation:** Compiles the code using all CPU threads.
	- `sudo make install`  
	    - **Explanation:** Installs Colm into the system.
	- `cd ~/snort`  
	    - **Explanation:** Returns to the Snort workspace.

```bash
echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/colm.conf
sudo ldconfig
cd ~/snort
```

- 💡 **`Colm` Library Path Update Explanation**:
	- `echo "/usr/local/lib" | sudo tee /etc/ld.so.conf.d/colm.conf`  
	    - **Explanation:** Creates a file to add `/usr/local/lib` to the shared library lookup paths.
	- `sudo ldconfig`  
	    - **Explanation:** Reloads dynamic linker cache to include Colm's libraries.
	- `cd ~/snort`  
	    - **Explanation:** Returns to your Snort working directory.


<br/>

- **`Ragel`**
	- A **state machine compiler** used by Snort’s protocol parsers. Requires Colm to be installed beforehand.

```bash
wget https://www.colm.net/files/ragel/ragel-7.0.4.tar.gz
tar -xzvf ragel-7.0.4.tar.gz
cd ragel-7.0.4
./configure --with-colm=/usr/local
make -j$(nproc)
sudo make install
cd ~/snort
```

- 💡 **`Ragel` Code Explanation**:
	- `wget https://www.colm.net/files/ragel/ragel-7.0.4.tar.gz`  
	    - **Explanation:** Downloads Ragel source code.
	- `tar -xzvf ragel-7.0.4.tar.gz`  
	    - **Explanation:** Extracts the contents.
	- `cd ragel-7.0.4`  
	    - **Explanation:** Enters the extracted source folder.
	- `./configure --with-colm=/usr/local`  
	    - **Explanation:** Configures Ragel to use the installed Colm library.
	- `make -j$(nproc)`  
	    - **Explanation:** Compiles Ragel using all available CPU cores.
	- `sudo make install`  
	    - **Explanation:** Installs Ragel system-wide.
	- `cd ~/snort`  
	    - **Explanation:** Returns to your Snort workspace.

```bash
sudo ldconfig
```

- 💡 **`ldconfig` Explanation**:
	- `sudo ldconfig`
	    - **Explanation:** Ensures newly installed libraries (like Ragel and Colm) are usable by applications.


<br/>

- **`Hyperscan (High-performance regex engine)`**
	- A **high-performance regex matching library** that Snort 3 uses to accelerate pattern matching in rules.

```bash
git clone https://github.com/intel/hyperscan.git
cd hyperscan
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j"$(nproc)" && sudo make install
cd ~/snort
```

- 💡 **`Hyperscan` Code Explanation**:
	- `git clone https://github.com/intel/hyperscan.git`  
	    - **Explanation:** Clones Intel’s official Hyperscan repo.
	- `cd hyperscan`  
	    - **Explanation:** Navigates into the Hyperscan source directory.
	- `mkdir build && cd build`  
	    - **Explanation:** Creates and enters a new `build` directory (clean CMake practice).
	- `cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local ..`  
	    - **Explanation:** Configures a release-mode build with installation target `/usr/local`.
	- `make -j"$(nproc)"`  
	    - **Explanation:** Compiles Hyperscan with all CPU threads.
	- `sudo make install`  
	    - **Explanation:** Installs the Hyperscan libraries and headers.
	- `cd ~/snort`  
	    - **Explanation:** Returns to the main Snort project directory.


<br/>

- **`PulledPork3 (Rule manager for Snort 3)`**
	- A **Python 3-based rules manager** for Snort 3, automating downloading and maintaining rule sets from Snort.org or Talos.

```bash
git clone https://github.com/shirkdog/pulledpork3.git ~/snort/pulledpork3
sudo mkdir -p /usr/local/bin/pulledpork3 /usr/local/etc/pulledpork3
sudo cp pulledpork3/pulledpork.py /usr/local/bin/pulledpork3/
sudo cp -r pulledpork3/lib/ /usr/local/bin/pulledpork3/
sudo chmod +x /usr/local/bin/pulledpork3/pulledpork.py
sudo cp pulledpork3/etc/pulledpork.conf /usr/local/etc/pulledpork3/
```

- 💡 **`PulledPork3` Code Explanation**:
	- `git clone https://github.com/shirkdog/pulledpork3.git ~/snort/pulledpork3`  
	    - **Explanation:** Clones the PulledPork3 rule manager into the Snort workspace.
	- `sudo mkdir -p /usr/local/bin/pulledpork3 /usr/local/etc/pulledpork3`  
	    - **Explanation:** Creates the binary and config directories for PulledPork3.
	- `sudo cp pulledpork3/pulledpork.py /usr/local/bin/pulledpork3/`  
	    - **Explanation:** Copies the main Python script into the executable directory.
	- `sudo cp -r pulledpork3/lib/ /usr/local/bin/pulledpork3/`  
	    - **Explanation:** Copies required Python libraries into the binary folder.
	- `sudo chmod +x /usr/local/bin/pulledpork3/pulledpork.py`  
	    - **Explanation:** Makes the script executable.
	- `sudo cp pulledpork3/etc/pulledpork.conf /usr/local/etc/pulledpork3/`  
	    - **Explanation:** Places the default configuration file into the appropriate location.


<br/>

### (1.4) Update Shared libraries

- **`Update Shared Libraries`**
	- Ensures the system is aware of all the new libraries you've installed (like `Hyperscan`, `Ragel`, `Colm`, etc.) so that Snort can link against them correctly.

```bash
sudo ldconfig
```

- 💡 **`ldconfig` Code Explanation**:
	- `sudo ldconfig`
	    - **Explanation:** Scans `/etc/ld.so.conf` and updates the system's runtime linker cache to recognize newly installed shared libraries.


<br/>

### (1.5) Install latest version of Snort 3
- **`Snort 3` (Latest Version)**
	- This builds and installs **Snort 3 from the official source** with support for performance optimization using `jemalloc`. This is the final and core step of installing Snort.

```bash
git clone https://github.com/snort3/snort3.git
cd snort3
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DENABLE_JEMALLOC=ON
make -j"$(nproc)" && sudo make install
```

- 💡 **`Snort 3 Install Code Explanation`**:
	- `git clone https://github.com/snort3/snort3.git`  
	    - **Explanation:** Clones the latest Snort 3 source code from the official GitHub repository.
	- `cd snort3`  
	    - **Explanation:** Changes into the cloned Snort source directory.
	- `mkdir build && cd build`  
	    - **Explanation:** Creates and enters a separate build directory (recommended for CMake builds).
	- `cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local -DENABLE_JEMALLOC=ON`  
	    - **Explanation:** Configures the Snort build, enabling `jemalloc` for improved memory performance, and sets install path to `/usr/local`.
	- `make -j"$(nproc)"`  
	    - **Explanation:** Compiles Snort using all available CPU threads for faster building.
	- `sudo make install`  
	    - **Explanation:** Installs the Snort binary and supporting files into `/usr/local`.


<br/>

---

## **(Step 2) Testing Snort 3**


<br/>

### (2.1) Verify Snort is Installed

- Confirm where `snort` was installed
```bash
sudo find /usr/local/ -type f -name snort
```
- Expected output: `/usr/local/bin/snort`
- ![image](https://github.com/user-attachments/assets/0c083dc3-fbac-4178-958f-00979ad3d5b6)

- Then test it directly:
```bash
/usr/local/bin/snort -V
```
- If that works, it means Snort was installed correctly.
- ![image](https://github.com/user-attachments/assets/691a1476-53d2-4d89-81ac-d2beb8c0f4f1)


<br/>

### (2.2) Remove old system path assumption

- Snort 2 installs to `/usr/sbin/snort`, but Snort 3 installs to `/usr/local/bin/snort`. You can create a symbolic link for convenience:
```bash
sudo ln -s /usr/local/bin/snort /usr/sbin/snort
```
- Now `snort -V` and `snort -c ...` will work like expected.
- ![image](https://github.com/user-attachments/assets/05772256-75cc-4d0a-8ccc-cb931c8e4206)


<br/>

### (2.3) Disable LRO & GRO using a service

```bash
sudo tee /etc/systemd/system/disable-lro-gro.service > /dev/null <<EOF
[Unit]
Description=Disable LRO and GRO for network interface
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ethtool -K enp0s3 gro off
ExecStart=/sbin/ethtool -K enp0s3 lro off

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable --now disable-lro-gro.service
```

- Verify with `cat /etc/systemd/system/disable-lro-gro.service`
- ![image](https://github.com/user-attachments/assets/d3a3ed6e-fcef-4233-98d5-896d9cfd9c0c)
- Check the status of the service with `sudo service disable-lro-gro status`
- ![image](https://github.com/user-attachments/assets/f164e465-5366-41c6-a68c-3798d07396ca)
- The output confirms that your service **ran successfully and completed its task**, even though it shows as `inactive (dead)` now. This is normal behavior for a **`Type=oneshot`** systemd service, which is designed to:
	- Run once at boot (or when manually triggered),
	- Perform its job (in this case, disabling GRO and LRO),
	- Then exit and remain in a non-running state.
- **Key indicators of success:**
	- `status=0/SUCCESS` on both `ExecStart` lines → the `ethtool` commands ran without errors.
	- `Deactivated successfully` and `Finished disable-lro-gro.service` → systemd considers it a clean execution.
	- It was triggered at boot or manually (check `sudo journalctl -u disable-lro-gro` for more logs).

- To verify **GRO** and **LRO** are **currently disabled** on `enp0s3`: `sudo ethtool -k enp0s3 | grep -E 'generic-receive-offload|large-receive-offload'`. You should see an output like:
	- `generic-receive-offload: off`
	- `large-receive-offload: off`
- ![image](https://github.com/user-attachments/assets/4ce9b5ae-5610-4139-b2c3-d42d33d1c693)
- That confirms the service worked and **successfully disabled both GRO and LRO** on your interface `enp0s3`. `large-receive-offload: off [fixed]` means LRO is **permanently disabled by the driver or hardware** and **cannot be re-enabled**, which is fine and sometimes preferred in security or low-latency environments.


<br/>

### (2.4) Creating a Custom Snort Rule

- Now that the foundation is set, we will create a custom Snort rule to detect ICMP traffic, then ping our Ubuntu VM from our Rocky Linux VM. To begin, create a directory using the command `sudo mkdir /usr/local/etc/rules`.
- ![image](https://github.com/user-attachments/assets/c16cabd8-4f97-4a45-b2ad-f2e2cdf43061)
- Now we'll create a file named `local.rules` within the directory. Run the command `sudo nano /usr/local/etc/rules/local.rules`. You'll be brought to this screen:
- ![image](https://github.com/user-attachments/assets/a65c4461-02af-4b21-ad1c-94f139985756)
- This is a plain file that we can use to start creating rules for Snort to utilize. Paste in `alert icmp any any -> any any (msg:"ICMP Detected"; sid:1000001;)`. Here is what it does:
	- **Rule Header**
		- **`alert`** - This is the _action_ Snort will take if the rule matches traffic. When a match is found, Snort will log the packet and generate an alert.
		- **`icmp`** - This is the _protocol_ to match. ICMP is used by utilities like `ping`.
		- **`any any` (source IP and port)** - Match ICMP packets from _any_ source IP and _any_ port (ICMP doesn't use ports, so this is a placeholder).  
		- **`->`** - Indicates the direction of traffic. From source to destination.  
		- **`any any` (destination IP and port)** - Match ICMP packets to _any_ destination IP and _any_ port. Again, ports are not used for ICMP, but required in rule syntax.
	- **Rule Options (in parentheses)**
		- **`msg:"ICMP Detected";`** - The message that will appear in the Snort alert log.  
		- **`sid:1000001;`** - This is the _Snort ID_ (SID), a unique identifier for the rule.
	- Example:
	- ![image](https://github.com/user-attachments/assets/c9839299-1948-42d4-96d7-5955906b2931)
- `ctrl + o` to save, press `Enter` to confirm, then `ctrl + x` to exit.
- ![image](https://github.com/user-attachments/assets/5ff128d4-1045-4246-a860-150332f5cf05)
- Basically, it will detect if Ubuntu is pinged. Now we need to test the custom rule. Run this command:

```bash
sudo snort -c /usr/local/etc/snort/snort.lua -R /usr/local/etc/rules/local.rules -i <interface> -A alert_fast
```
- This command launches Snort 3 with both a full configuration and a specific rules file. Here's what each part does:
	- `sudo` - Runs the command with root privileges. Snort needs elevated access to sniff network traffic. Without `sudo`, Snort likely won't be able to bind to the network interface or access certain files.
	- `snort` - This calls the Snort binary (Snort 3) to execute the program.
	- `-c /usr/local/etc/snort/snort.lua` - This tells Snort to use the **main configuration file**, which in Snort 3 is written in Lua (`.lua` file). This file contains global settings like network variables, paths, plugins, and output options.
	- `-R /usr/local/etc/rules/local.rules` - This loads a **specific rules file** (`local.rules`) directly into the runtime. Instead of loading a full set of default or community rules, this targets just your custom rules file for quick testing.
	- `-i <interface>` - Specifies the **network interface** to monitor, like `eth0`, `enp0s3`, or `ens33` (replace `<interface>` accordingly).
	- `-A alert_fast` - Sets the **alert output mode** to "fast", which logs alerts in a simple, human-readable format.
- ![image](https://github.com/user-attachments/assets/7c36ce94-5020-4f3b-8d50-6a5ce419edd2)
- ![image](https://github.com/user-attachments/assets/6ffcb375-db98-4218-ac0a-382e238b6bc9)
- Now Snort is listening in on that traffic, using the network adapter `enp0s3`. Now let's head to our other VM and ping Ubuntu while it's listening.

- From Rocky Linux run:

```bash
ping 10.0.2.33 -c4
```
- ![image](https://github.com/user-attachments/assets/a38d87a1-b3b4-4aa2-a3a5-789afdc76d81)

- Now press `ctrl + c` to cancel the capture. After maybe 3-5 minutes, it will stop and then show that our custom rule works! Our Rocky Linux (IPv4 address `10.0.2.32`) pinged Ubuntu (IPv4 address `10.0.2.33`) 4 times!
- ![image](https://github.com/user-attachments/assets/f100f96a-a334-4813-8b48-739dbcb7172a)

> 💡 **Why You See 8 Alerts When You Only Sent 4 Pings** 
> - This is expected behavior due to how ICMP works:
> 1. Each `ping` **sends an ICMP Echo Request**.
> 2. The target host replies with an **ICMP Echo Reply**.
> So each ping produces:
> - **1 packet from you to the target** (Echo Request)
> - **1 packet from the target to you** (Echo Reply)
> ✅ **4 pings = 8 ICMP packets = 8 alerts**  
> - Snort logs both sides of the conversation unless told otherwise.

- Here's the rest of the output:
- ![image](https://github.com/user-attachments/assets/8ae95b0f-5a2a-4fd2-a091-9dbd0efec4c3)
- ![image](https://github.com/user-attachments/assets/4650fa02-d5ca-4bf2-9d39-e7e6d9209867)

- We can also modify our Snort configuration, so that next time, we don't have to specify the rule's location. To do that we'll head to Snort's configuration file with `sudo nano /usr/local/etc/snort/snort.lua`. It brings us to this screen (the "heart" of Snort):
- ![image](https://github.com/user-attachments/assets/ca9b2064-596d-4617-ac58-ea2b9a85f313)
- We need to find a field called `ips`. Find it with `ctrl + f` on your keyboard.
- ![image](https://github.com/user-attachments/assets/380d42f6-81c4-4f81-9fa7-8665bd5f8cf5)
- The options in green with the `--` before it have been commented out. Delete the `--` to activate the option. We'll do it for `enable_builtin_rules = true,`. Then underneath `-- (see also related path vars at the top of snort_defaults.lua)`, enter `include = "/usr/local/etc/rules/local.rules",`. Save and exit with `ctrl + o`, `Enter`, then `ctrl + x`.
- ![image](https://github.com/user-attachments/assets/2e410002-ba09-4a75-add4-737300877022)
- Let's run the rule again, but remove the rule path. Run:

```bash
sudo snort -c /usr/local/etc/snort/snort.lua -i <interface> -A alert_fast
```

- ![image](https://github.com/user-attachments/assets/86ccb519-c5d5-4f07-8348-a690d3ae9714)

- Now ping from your Rocky Linux again. Then exit the capture from Ubuntu with `ctrl + c`. You'll see it works if you get similar results as before:
- ![image](https://github.com/user-attachments/assets/cae5fcbf-d3ac-4590-9f41-e65e00e55596)

---

## **(Step 3) Using PulledPork for Free Rules** ⚠️ *Optional Challenge – Incomplete*

> 🚫 **This section is currently incomplete.**
> 
> - While attempting to use PulledPork 3 to manage Snort 3 rules, I ran into an issue that prevented full completion of this step. The exact cause is unclear, and after several hours of troubleshooting, I’ve decided to pause this part of the project for now.
> 
> - I’m keeping this section in place as an **optional challenge** for others who may want to experiment further. If you’re working through this project and manage to get it functioning as intended, I’d be glad to hear your solution!


<br/>

### (3.1) Verify installation

- To expand our ruleset, we will be using "Pulled Pork" which automatically grabs the rules for us to use. We already downloaded it in earlier steps. We'll verify the installation with  `/usr/local/bin/pulledpork3/pulledpork.py -V`.
- ![image](https://github.com/user-attachments/assets/8666b289-3d24-4c74-b3f7-32e230b3b5b6)


<br/>

### (3.2) Edit Configuration File

- It's a success! Now we'll modify the configuration file. Run:

```bash
sudo nano /usr/local/etc/pulledpork3/pulledpork.conf
```

- ![image](https://github.com/user-attachments/assets/35b6200a-e01f-48c3-aeda-91038b6175ed)
- We're immediately presented with which ruleset do you want to download and they recommend to only choose one. We'll select `registered_ruleset` and change the value to `true`.
- ![image](https://github.com/user-attachments/assets/ab6d4b61-7216-4060-be27-b177c4a010a0)
- Below it says we require something that should seem familiar. Our `oinkcode`. Copy and paste yours in here from the steps we completed earlier.
- ![image](https://github.com/user-attachments/assets/ee957472-9813-4c1e-a95c-d4b47becff52)
- Below the `oinkcode` we have the option to download an optional blocklist by setting it from `false` to `true`, but we won't be using it, so we also need to comment out `blocklist_path = /usr/local/etc/lists/default.blocklist`.
- ![image](https://github.com/user-attachments/assets/57eab06d-40ba-471a-b1cc-f0a18e79cc61)
- Next we need to remove from `#` from `snort_path = /usr/local/bin/snort`
- ![image](https://github.com/user-attachments/assets/f1182cc1-d75e-4f5f-9443-20b3bfeb4a81)
- Scroll down a bit more to `# Local Rules files` and remove the comment for `local_rules = /usr/local/etc/rules/local.rules, /another/path/to/more/rules`. Then remove `, /another/path/to/more/rules`. Then save and exit out of nano.
- ![image](https://github.com/user-attachments/assets/2f858f1e-9e81-45fb-930a-94130275694f)


<br/>

### (3.3) Edit pulledpork.py

- For pulled pork to work, we need to hardcode the version of the rules we want to download. Run:

```bash
sudo nano /usr/local/bin/pulledpork3/pulledpork.py
```

- ![image](https://github.com/user-attachments/assets/390d37bd-b137-45fa-8ddf-ebb160325c6f)
- Now we need to scroll to where it says `RULESET_URL_SNORT_REGISTERED = 'https://snort.org/rules/snortrules-snapshot-<VERSION>.tar.gz'`.
- ![image](https://github.com/user-attachments/assets/f8075189-c2ac-4e3f-9743-4f317e4640f9)
- For the `<VERSION>`, we will replace it with the latest version out. Find it by heading to the **[Snort Rules Download section](https://www.snort.org/downloads#rules)**. Here we can see it's `31470`.
- ![image](https://github.com/user-attachments/assets/7ec120d6-c27c-4646-9ed9-9af4212bbaef)
- Replace `<VERSION>` with `31470`, then save and exit.
- ![image](https://github.com/user-attachments/assets/d4a8dd26-9233-4a66-a1f1-86989a87efdc)
- The last step we need to take before running is make a directory called `so_rules` or we will run into errors. Run:

```bash
sudo mkdir /usr/local/etc/so_rules
```

- You can then verify with:

```bash
ls /usr/local/etc/
```

- ![image](https://github.com/user-attachments/assets/34ee5dea-c102-4bcf-bb1c-37d84a056bfd)
- Great! Now let's run it and see what happens!


<br/>

### (3.4) Run PulledPork 3

- Run PulledPork 3:

```bash
sudo /usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf
```

- ![image](https://github.com/user-attachments/assets/ef3c3730-25e4-49e4-952e-905c778e2a8b)


<br/>

### (3.5) Integrating Pulled Pork Rules into Snort

- Now we need to modify the Snort configuration to point to PulledPork's rules. Run:

```bash
sudo nano /usr/local/etc/snort/snort.lua
```

- Search for `ips` again with `ctrl + f`.
- ![image](https://github.com/user-attachments/assets/5cf862b0-57a9-4bff-85b5-0d2fbc3907be)
- Once there, change `include = "/usr/local/etc/rules/local.rules",` to **`include = "/usr/local/etc/rules/pulledpork.rules",`**. Then save and exit.
- ![image](https://github.com/user-attachments/assets/fc9f3593-975c-4efa-bc9f-381c51292cf1)
- You can also see a few of the rules by rules by running:

```bash
cat /usr/local/etc/rules/pulledpork.rules | less
```

- ![image](https://github.com/user-attachments/assets/9aaf1dd9-74fc-4d87-8a34-e5e40e4206ac)


<br/>

### (3.6) Run Snort to Test PulledPork

- Run:

```bash
snort -c /usr/local/etc/snort/snort.lua --plugin-path /usr/local/etc/so_rules/
```

- AAAAAAAAANNNNDD..... It failed.... 😭 I spent a few more hours trying to troubleshoot this but unfortunately I don't have the time to keep focusing on this particular project much longer, as it has already been a week and I have more that I NEED to complete. However if you figure this section out, please reach out to me and let me know. The good news is that Snort 3 ITSELF actually works. Which means we can move on to the last part which is investigating a malicious pcap. Here is the failure output:
- ![image](https://github.com/user-attachments/assets/22818991-ac28-414f-af85-8d14bcd7f043)
- ![image](https://github.com/user-attachments/assets/8e1d123d-3847-49aa-b061-9a14b8e209c8)
- ![image](https://github.com/user-attachments/assets/4e6b8710-5624-4449-8d70-c909dd5fd77a)
- Skipping to the bottom:
- ![image](https://github.com/user-attachments/assets/ca420bdc-7d71-487f-88ac-6ed042685614)
- ![image](https://github.com/user-attachments/assets/73eab4f6-6efe-456f-a7ea-a041312d1453)
- Very disappointing that this part can not be completed at this time, but I may return to it at a later date when I'm better equipped. Moving on to the final part!

---

## **(Step 4) Generating Signatures from a PCAP**  *⚠️ Optional Challenge – Incomplete*


<br/>

### (4.1) Download pcap

- One practical use of an IDS is to analyze packet captures (PCAPs) and identify malicious activity by generating signatures from the traffic. To demonstrate this, we will download a pcap from `malware-traffic-analysis.net`. **[Here](https://www.malware-traffic-analysis.net/2022/02/23/)** is the exercise we will be downloading from:
- ![image](https://github.com/user-attachments/assets/d0b67bf5-d616-4e9e-9740-e8c7b09ef037)
- ![image](https://github.com/user-attachments/assets/f6086b5a-c0be-4be3-9448-5355aa0cb182)
- To begin, we'll make a directory called test with `mkdir ~/test`. Then enter it with `cd ~/test`.
- ![image](https://github.com/user-attachments/assets/850a0fe5-be59-492b-8101-738850bcdb8d)
- Download the pcap with this command. `ls` will confirm in red that it has been downloaded.
```bash
wget https://www.malware-traffic-analysis.net/2022/02/23/2022-02-23-traffic-analysis-exercise.pcap.zip
ls
```

- ![image](https://github.com/user-attachments/assets/42eacaf0-b704-4410-94ae-9520fc9cc36b)


<br/>

### (4.2) Unzip pcap

- Unzip the file with:

```bash
unzip 2022-02-23-traffic-analysis-exercise.pcap.zip 
```

- The password will be `infected_20220223`.
- ![image](https://github.com/user-attachments/assets/a6ac33ad-6aa5-4858-a911-1253a93418c8)


<br/>

### (4.3) Run Snort and analyze the pcap

- Now run:

```bash
snort -c /usr/local/etc/snort/snort.lua --plugin-path /usr/local/etc/so_rules/ -r 2022-02-23-traffic-analysis-exercise.pcap -A alert_fast -q
```

- You will see a large output! In fact it is so much that we will just create a file and put all of this data within that file.
- ![image](https://github.com/user-attachments/assets/a790781a-30ed-4b76-8d67-63ae0c40d4f1)


<br/>

### (4.4) Attempting to Parse Results

- Run 

```bash
snort -c /usr/local/etc/snort/snort.lua --plugin-path /usr/local/etc/so_rules/ -r 2022-02-23-traffic-analysis-exercise.pcap -A alert_fast -q > pcap-signatures.txt
```

- ![image](https://github.com/user-attachments/assets/f899fa51-3144-495b-8769-99a99d674009)

- Now run this to make sure it is all there:

```bash
cat pcap-signatures.txt
```

- ![image](https://github.com/user-attachments/assets/ea187074-8dc7-4f18-aec1-c1b207ff98ab)
- Excellent! It's all there. Now we can cut this data up to make it more presentable. Try this:

```bash
cat pcap-signatures.txt | cut -d "]" -f 1
```

- ![image](https://github.com/user-attachments/assets/7ea49049-da5e-4f91-af37-12c0d7648596)
- This is the same data, but just sliced up. You can probably guess that this is still not enough to find what we want. It's also cutting away too much. So we'll alter it further:

```bash
cat pcap-signatures.txt | cut -d "]" -f 3 | cut -d "[" -f 1
```

- ![image](https://github.com/user-attachments/assets/623cd61d-c4ba-4176-8afd-2bfdcec8360b)
- Better... but we can go even further beyond! 🔥 ✂️
- <img src="https://github.com/user-attachments/assets/42ccbad0-1121-4c0e-a301-75642045c665" width="100%" height="100%">

- Try this:

```bash
cat pcap-signatures.txt | cut -d "]" -f 3 | cut -d "[" -f 1 | cut -d '"' -f 2 | sort | uniq -c | sort -nr
```

- ![image](https://github.com/user-attachments/assets/e1290272-fa2d-449b-89e0-e71f5dc30ec7)
- What's this?? How do we not have the same output as in the **[video](https://www.youtube.com/watch?v=j7Wapw3Gxvg)** this project is based on??
- ![image](https://github.com/user-attachments/assets/390f4320-0986-451c-8000-d447f4600d57)
- I also ran `grep -i malware pcap-signatures.txt` to see if anything pops up, but there were no results! Where is it?!
- <img src="https://github.com/user-attachments/assets/245994db-90fe-41b7-8d5d-83fe25ab4f63" width="100%" height="100%">
- 🤔 Could this have to do with the errors we received in **Step 3** and we were doomed all along? Has this pcap from 2022 been altered?


<br/>

> 🧠 **Closing thoughts**
> - It was here when I realized that I may have strayed away from following the tutorial video TOO much, and that this is where I will have to end the project.
> - I wanted the latest and greatest version of Snort 3, with all of the features installed and updated packages. However, not following the exact instructions of the video may have set me up for failure and lead to misconfigurations along the way.
> - Let's wrap up and see what went right and wrong in the project.

---

## **Final Thoughts & Lessons Learned**

This project was meant to be a hands-on guide for building **Snort 3 from source**, but it became more of a lesson of **patience, troubleshooting, and sticking to what works**.

Never has there been a better time to use the phrase "If it ain't broke, don't fix it". Trying to "upgrade" Snort 3 proved to be more than I could handle at the moment.

That being said, I'm still glad I attempted it in my isolated lab environment. If this were for production or personal use, I would stick with a more stable version of Snort and it's dependencies, rather than the "bleeding-edge".

### ✅ What Went Right

- Successfully compiled and installed Snort 3 from source (v3.7.4.0) with the latest required dependencies and plugins (DAQ, gperftools, flatbuffers, etc.)
- Created my first major bash script to automate a portion of the project.
- VirtualBox NAT Networking went smoother than it has for me in other projects I've attempted.
- We learned about altering configuration files.
- Despite failing a few steps, I would say my troubleshooting skills have increased, as it does with every project.
- I'd also like to think that I documented each major step clearly, including terminal output, screenshots, and explanations. However, that is for you to decide 😀

### ⚠️ What Went Wrong

- PulledPork 3 integration failed near the end. The root cause remains unclear and may involve version mismatches or misconfigured paths.
- Signature generation and analysis of our PCAP produced empty or incorrect results, possibly due to:
    - Incomplete or outdated rules
    - Incorrect usage of `snort.lua` and plugin paths
    - Deviations from the original tutorial’s install process
- Snort 3 works, but it's slow for a reason I did not get a chance to figure out.
- The time estimated to complete this project was off, and next time I should set a deadline.

### 💡 Lessons Learned

- **Source builds have the potential to be fragile**, especially for large tools like Snort with many dependencies and plugins. Package managers exist for a reason.
- Following tutorials too rigidly or too loosely can both lead to breakage, so it's best to find a balance.
- Projects like these are not failures, they’re learning experiences designed to get you ready for your next level of understanding.
- Sometimes, it’s okay to walk away and come back later, especially when diminishing returns set in.

---

## 📌 Final Status


<br/>

✅ **Snort 3 installed and operational**  
⚠️ **Rule management and signature analysis incomplete**  
🧠 **Project paused, not abandoned**

Feel free to pick up where I left off. And if you solve the mystery behind Step 3 or Step 4, reach out! I’d love to know how you cracked it.

---
