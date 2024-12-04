This guide will walk you through setting up a Debian VM in VirtualBox and configuring it with Kali Linux tools to create a safe, isolated environment for practicing CTF hacking challenges.

---

## Prerequisites

- **VirtualBox**: [Download and install VirtualBox](https://www.virtualbox.org/wiki/Downloads) for your system (Windows, MacOS, or Linux).
- **Debian ISO**: [Download the latest Debian ISO](https://www.debian.org/distrib/) for a clean Debian installation.

---

## 1. Setting Up the VirtualBox VM with Debian

### Step 1: Create a New Virtual Machine

1. Open VirtualBox and click **New**.
2. Enter a **Name** for your VM (e.g., "DebianCTF").
3. Choose **Linux** as the Type and **Debian (64-bit)** as the Version.
4. Click **Next**.

### Step 2: Configure the VM Settings

1. **Memory (RAM)**: Allocate at least **2 GB (2048 MB)** for better performance.
2. **Hard Disk**: Choose **Create a virtual hard disk now** and click **Create**.
3. Select **VDI (VirtualBox Disk Image)** as the file type and **Dynamically allocated** for storage.
4. Choose a disk size (20 GB is recommended) and click **Create**.

### Step 3: Load the Debian ISO

1. With the VM selected, go to **Settings > Storage**.
2. Click on the **Empty** disk under **Controller: IDE** and select the **DVD icon** on the right.
3. Choose **Choose a disk file** and select the Debian ISO you downloaded.

### Step 4: Start the VM and Install Debian

1. Start the VM by selecting it and clicking **Start**.
2. Choose **Graphical Install** and follow the setup prompts:
   - Select your **language**, **location**, and **keyboard** layout.
   - Set up a **root password** and create a regular user account.
   - Choose **Guided – Use Entire Disk** for partitioning.
3. Let the installation complete and remove the ISO when prompted to restart.

After rebooting, log in with your user credentials to access your new Debian environment.

---

## 2. Configuring Kali Linux Repositories on Debian

By adding the Kali Linux repositories to Debian, you can access Kali’s penetration testing tools directly from Debian.

### Step 1: Add Kali Repositories

1. Open a terminal.
2. Edit the sources list by typing:

   ```bash
   sudo nano /etc/apt/sources.list
   ```
- **`sudo`**: Runs the command with superuser privileges.
- **`nano`**: Opens the Nano text editor.
- **`/etc/apt/sources.list`**: This file contains a list of repositories used by `apt` to download packages. Adding the Kali repositories here allows us to install Kali tools on Debian.



3. Add the following Kali repositories to the bottom of the file:

   ```plaintext
   deb http://http.kali.org/kali kali-rolling main non-free contrib
   ```

4. Save the file (`Ctrl + O` to write out, `Enter` to confirm, and `Ctrl + X` to exit).

### Step 2: Import Kali’s Package Keys

To ensure packages from the Kali repositories are trusted, add Kali’s public key:

```bash
wget -q -O - https://archive.kali.org/archive-key.asc | sudo apt-key add -
```
- **`wget -q -O - <URL>`**: Downloads the Kali repository key and outputs it to standard output (`-O -`).
- **`sudo apt-key add -`**: Adds the downloaded key to the list of trusted keys, which lets Debian verify that the packages from Kali are authentic.


### Step 3: Update and Upgrade

Run the following commands to refresh the package list and update any available packages:

```bash
sudo apt update && sudo apt upgrade -y
```

- **`sudo apt update`**: Refreshes the list of available packages from the configured repositories.
- **`&&`**: Executes the next command only if the previous one succeeds.
- **`sudo apt upgrade -y`**: Installs the latest versions of all installed packages. `-y` automatically confirms the upgrade.

---

## 3. Installing t00lbox

Run the following commands to install t00lbox on your machine and copy the inital resources.yml file containing the "database" for t00lbox.
```bash
sudo apt install go curl
mkdir ~/.go
echo "GOPATH=$HOME/.go" >> ~/.bashrc
echo "export GOPATH" >> ~/.bashrc
echo "PATH=\$PATH:\$GOPATH/bin # Add GOPATH/bin to PATH for scripting" >> ~/.bashrc
source ~/.bashrc
go install github.com/burgr033/t00lbox/cmd/t00lbox
curl https://raw.githubusercontent.com/burgr033/t00lbox/refs/heads/main/resources.yml --create-dirs --output ~/.config/t00lbox/resources.yml
```

## 4. Installing Essential Hacking Tools

With the Kali repositories added, you can now install tools commonly used in CTFs.

### Step 1: Install Packages

Use the following command to install essential tools:

```bash
sudo apt install -y nmap openvpn gobuster john seclists
```

- **`sudo apt install -y <packages>`**: Installs the specified packages. `-y` automatically confirms the installation, avoiding the need for user prompts.


### Tool Overview

- **Nmap**: Network scanner to discover hosts and open ports.
- **OpenVPN**: VPN client, essential for connecting to CTF networks.
- **Gobuster**: Directory brute-forcing tool, useful for discovering hidden paths on websites.
- **John the Ripper**: Password-cracking tools for brute-force and dictionary attacks.
- **SecLists**: Collection of wordlists for brute-forcing and enumeration.

---

## 5. Setting Up OpenVPN for Hack The Box or TryHackMe

Both Hack The Box and TryHackMe use OpenVPN to provide access to their CTF networks. Here’s how to set it up.

### Step 1: Download the OpenVPN Profile

1. Log in to your Hack The Box or TryHackMe account.
2. Download the `.ovpn` configuration file from their VPN section.

### Step 2: Move the Profile to a Convenient Location

Move the downloaded file to `/etc/openvpn` for easy access:

```bash
sudo mv ~/Downloads/<yourfile>.ovpn /etc/openvpn/
```

- **`mv <source> <destination>`**: Moves a file from `<source>` to `<destination>`.
- **`~/Downloads/<yourfile>.ovpn`**: The file path for the downloaded VPN profile (replace `<yourfile>` with the actual file name).
- **`/etc/openvpn/`**: A commonly used directory for OpenVPN configuration files, so they are easy to locate.

### Step 3: Start the OpenVPN Connection

To connect to the CTF network, use the following command:

```bash
sudo openvpn /etc/openvpn/<yourfile>.ovpn
```

- **`sudo openvpn <config file>`**: Starts OpenVPN using the specified configuration file (replace `<yourfile>` with your VPN profile). This command connects the VM to the CTF network.


This will start the OpenVPN client, and you should see a successful connection message if everything is set up correctly. Keep this terminal open while working on CTF challenges to stay connected.

---

## 6. Useful Tips

### Taking Snapshots

After setting up your VM, **take a snapshot**. This way, you can restore the VM to this clean state if needed.

- In VirtualBox, go to **Machine > Take Snapshot**.

### Network Settings for Your VM

- **NAT (Network Address Translation)** is typically selected by default and provides internet access to your VM while keeping it more secure.
- **Bridged Adapter**: Allows the VM to act as a separate device on your network, which can be useful for certain network tasks, like scanning other devices on your local network.

### Keeping Debian Updated

Periodically, update your Debian system and installed tools to have the latest security patches and features:

```bash
sudo apt update && sudo apt upgrade -y
```

---

## Conclusion

You now have a Debian VM set up in VirtualBox with essential Kali Linux tools, ready to participate in CTF challenges. With this environment, you can explore platforms like Hack The Box and TryHackMe safely, practicing your penetration testing skills in an isolated and secure setup.
