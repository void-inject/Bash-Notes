[2-FC_TP](2-FC_TP.md) [README](Bash-Notes/README.md) [4-Additional_Tools](4-Additional_Tools.md)

Tags: #bash  #linux  #shell-scripting  #kali #lab #docker #virtualization 

---

# Setting Up a Hacking Lab

This guide will walk you through setting up a secure and functional hacking lab environment with essential tools and an intentionally vulnerable target system. The lab will be hosted in a virtualized environment, minimizing risks to your primary system and network.

## Minimum Virtual Machine Requirements

Ensure your virtual machine meets the following specifications:

- **RAM**: At least 4GB
- **Storage**: At least 40GB
- **Internet Connection**: Required for installation and updates

### Guidelines for a Secure Lab Setup

- **Isolate the Lab**: Avoid connecting the lab directly to the internet; use NAT or host-only networking.
- **Virtualization**: Use a hypervisor such as VirtualBox, VMware, or GNOME Boxes.
- **Frequent Snapshots**: Regularly snapshot your virtual machines to restore them if needed.

---

## A) Setting Up Kali Linux

Kali Linux will serve as:

1. The host machine to manage and run the lab environment.
2. The attacking machine to execute penetration tests and other attacks.

### Step 1: Installing Kali Linux

Follow the [official Kali installation guide](https://www.kali.org/docs/installation/) to set up the virtual machine. After installation, log in and update the system with the following commands:

```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt dist-upgrade -y
```

> **Note:** The `sudo` password is the same as the one used during login.

### Step 2: Switching to Bash (Optional)

Kali Linux defaults to Z Shell (zsh). To switch to Bash:

```bash
sudo usermod --shell /bin/bash <your-username>
su - <your-username>
```

### Step 3: Installing Additional Tools

Refer to the [4-Additional_Tools](https://chatgpt.com/c/4-Additional_Tools.md) for optional tool installations, or proceed with the automated deployment method described below.

---

## B) Setting Up the Target Environment

The target environment contains intentionally vulnerable systems. Setting up manually helps you understand its core components. Alternatively, use the automated method for quicker deployment.

### Automated Deployment

```bash
cd ~
git clone https://github.com/void-inject/ACME-Infinity-Servers.git
cd ACME-Infinity-Servers
sudo make init
```

> **Note:** Running `sudo make init` installs all required tools automatically, skipping manual setup.

### Installing Docker

Docker is essential for deploying target containers. Follow these steps:

#### Step 1: Install Dependencies

```bash
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
```

#### Step 2: Add Docker’s GPG Key

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### Step 3: Add Docker Repository

```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

#### Step 4: Update Repository List

```bash
sudo apt update -y
```

#### Step 5: Install Docker and Docker Compose

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

#### Step 6: Verify Installation

```bash
sudo docker compose --help
```

#### Step 7: Enable Docker on Boot

```bash
sudo systemctl enable docker --now
```

#### Optional: Run Docker Commands Without `sudo`

```bash
sudo usermod -aG docker $USER
```

> **Note:** Log out and log back in to apply group changes.

### Deploying the Target Environment

```bash
cd ~
git clone https://github.com/void-inject/ACME-Infinity-Servers.git
cd ACME-Infinity-Servers
sudo make deploy
```

Deployment will take a few minutes. Use the following command to monitor progress:

```bash
tail -f /var/log/lab-install.log
```

This command provides a live view of installation logs.

### Verifying the Deployment

Run the following to ensure the lab is operational:

```bash
sudo make test
```

Expected output:

```plaintext
Lab is up
```

Alternatively, check the status:

```bash
sudo make status
```

---

## C) Managing the Lab Environment

### Shutting Down

Turn off all containers when not in use:

```bash
sudo make teardown
```

### Deleting the Environment

To remove all containers and images:

```bash
sudo make clean
```

### Rebuilding the Environment

This process shuts down, deletes, and redeploys the lab:

```bash
sudo make rebuild
```

### Accessing Lab Machines

To access an individual lab machine shell:

```bash
sudo docker exec -it <machine-name> bash
```

---

## Additional Tips

1. **Snapshot Regularly:** Create snapshots after major changes to your lab environment.
2. **Network Configuration:** Use virtual networks like NAT or host-only for added security.
3. **Documentation:** Maintain notes on commands and configurations for future reference.

With this setup, you’re ready to start exploring and practicing cybersecurity techniques safely.