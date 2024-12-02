Status: On-Going

Tags: #bash #linux #shell 

Links: [2-FC_TP](2-FC_TP.md) [README](Scripting/Bash/0-Notes/README.md) 
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

___
## References:

Created:: 2024-12-02 15:15