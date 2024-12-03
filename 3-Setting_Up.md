Status: Complete

Tags: #bash #linux #shell 

Links: [2-FC_TP](2-FC_TP.md) [README](README.md) [4-Additional_Tools](4-Additional_Tools.md)
___

# Setting Up a Hacking Lab
For this Part, we will set up a lab environment containing hacking tools and an intentionally vulnerable target.

Virtual machine, i am gonna use, will require the following minimum specifications: 
- at least 4GB of RAM
- at least 40GB of storage 
- an internet connection

Follow these guidelines to reduce the risks associated with building and
operating a hacking lab:
- Avoid connecting the lab directly to the internet
- Deploy the lab in a virtual environment by using a hypervisor
- Take frequent snapshots of your virtual machine. Snapshots are backups of your virtual machine that allow you to restore it to a previous state
---
## A) Set-Up Kali
Kali machine will play two roles in the lab environment: 
- It will act as the host responsible for running the target networks and machines against which you’ll run your scripts
- It will serve as the hacking machine from which you’ll perform your attacks

To install Kali to your virtual machine follow [official kali guide](https://www.kali.org/docs/installation/)
After Installing and logging to your kali make sure to update it with these commands:
```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt dist-upgrade -y
```
When `sudo` command used it asks for password and it is same password you used to log-in

Newer Kali releases uses the Z Shell(zsh) by default and don't worry there is no much difference with Bash. But if you wanna change it to bash:
```bash
sudo usermod --shell /bin/bash <your username>
su - <your username>
```

---

## B) The Target Environment
We encourage you to set up your lab manually at least once by following the instructions in this section. This will allow you to familiarize yourself with the lab’s core components and practice running commands on the command line. However, if you ever need to redeploy the lab from scratch in a fresh installation of Kali, you can do so by:
```bash
cd ~
git clone https://github.com/void-inject/ACME-Infinity-Servers.git
cd ACME-Infinity-Servers
sudo make init
```
(If you run `sudo make init` you don't need to download Tools mentioned in [4-Additional_Tools](4-Additional_Tools.md) manually)
### Docker Installation:
**Install Required Dependencies**

`sudo apt install -y apt-transport-https ca-certificates curl software-properties-common`

**Add Docker's Official GPG Key**

`curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`

**Add the Docker Repository**

`echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian bookworm stable" | sudo tee /etc/apt/sources.list.d/docker.list`

**Update the Repository List**

`sudo apt update -y`

**Install Docker and Docker Compose** 

`sudo apt install docker-ce docker-ce-cli containerd.io -y`

**Verify Installation**

`sudo docker compose --help`

**Add Docker to Start-Apps**

`sudo systemctl enable docker --now`

Docker requires the use of _sudo_, which can get a little inconvenient. If you want to avoid having to enter _sudo_ before executing Docker-related commands, add the kali user to the docker Linux group:
`sudo usermod -aG docker $USER`

### Deploying Docker Containers
```bash
cd ~
git clone https://github.com/void-inject/ACME-Infinity-Servers.git
cd ACME-Infinity-Servers
sudo make deploy
```

The initial deployment of the lab environment will take a few minutes to complete. To monitor the progress of the installation, you’ll need to open a new terminal session and tail the logfile located under /var/log/​ lab-install.log, like so:

`tail -f /var/log/lab-install.log`

When the tail -f (follow) command is used against a file, it provides a live view of any new lines added to the end of the file. This is useful for keeping an eye on logfiles, which frequently have new information written to them.
### Testing and Verifying the Containers
Once the logfile indicates the process is complete, it should tell you whether the lab was set up correctly. We can also run a few commands to verify this. First, let’s execute a status check by using the make command, this time with the test argument. If all the checks pass, you should get the following output:

`$ sudo make test`

`Lab is up`

or you can run: `sudo make status`

---
## C) Managing the Lab
**Shutting Down**
When you’re not using the lab environment, turning it off is good practice. 
To shut down all the containers running in the lab, run the following:

`sudo make teardown`

**Deleteing**
To completely remove the lab environment from your Kali machine, you can run the clean command. This will destroy all containers and their images:

`sudo make clean`

**Rebuilding**
When we execute a rebuild, the lab will first shut down all running containers, delete volumes, and remove all container images before running a new deployment. To execute the rebuild, run the following command:

`sudo make rebuild`

**Accessing Individual Lab Machines**
To gain shell access to any individual lab machine, you can run the following Docker command:

`sudo docker exec -it <machine-name> bash`
___

Created:: 2024-12-02 15:15