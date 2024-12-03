Status: Complete

Tags: #linux #tools 

Links: [3-Setting_Up](3-Setting_Up.md) [README](README.md) 
___

# Additional Tools for Hacking
Most of tools we use are already installed in Kali, but still some of them should be installed manually.

First, create dedicated folder for tools:
```bash
cd ~
mkdir tools
```
move on to the folder and start installing tools mentioned below.

## _WhatWeb_

_WhatWeb_ can fingerprint websites and their application stack by using its database of known application signatures. _WhatWeb_ can also identify particular content management systems and blogging platforms (such as WordPress), web cameras, web application firewalls, and more. As of this writing, _WhatWeb_ has over 1,800 plug-ins

```bash
sudo apt-get install whatweb -y
```

We’ll use _WhatWeb_ to perform reconnaissance activities.

## _RustScan_

_RustScan_ is a lightning-fast port scanner written in the Rust programming language. Some claim that _RustScan_ can scan all 65,000 ports on a target in seconds!

we use _RustScan_'s docker version:
```bash
sudo docker pull rustscan/rustscan:2.1.1
```

We will use _RustScan_ for port scanning purposes

## _Nuclei_

_Nuclei_ is a vulnerability scanner written in the Go programming language by **ProjectDiscovery**, a company that builds popular open source hacking tools

```bash
sudo apt-get install nuclei -y
```

The first time you run _Nuclei_, it automatically creates a _nuclei-templates_ directory in the user’s home folder and downloads all the publicly available Nuclei templates. You can find those templates from official [github repo](https://github.com/projectdiscovery/nuclei-templates)

We will use Nuclei to find vulnerabilities in the lab, as well as for writing custom vulnerability checks.

## _dirsearch_

_dirsearch_ is a multithreaded tool used to find common paths on web servers.

```bash
sudo apt-get install dirsearch -y
```

We will use _dirsearch_ for information-gathering purposes

## _Linux Exploit Suggester 2_

The _Linux Exploit Suggester 2_ is a next-gen tool based on the original **Linux Exploit Suggester**. Written in Perl, it includes several exploits you can use to potentially compromise vulnerable Linux kernel versions

```bash
cd ~/tools
git clone https://github.com/jondonas/linux-exploit-suggester-2.git
```

test:
```bash
cd linux-exploit-suggester-2
perl linux-exploit-suggester-2.pl -h
```

We will use this tool to enumerate kernel exploits

## _Gitjacker_

_Gitjacker_ is a data-extraction tool that targets web applications whose .git directory has been mistakenly uploaded. Before you can install _Gitjacker_, you’ll first need to install _jq_, a command line JSON processor:

```bash
sudo apt-get install jq -y
```

```bash
cd ~
curl -s "https://raw.githubusercontent.com/liamg/gitjacker/master/scripts/install.sh" | bash
mv ./bin/gitjacker ~/tools/gitjacker
rmdir ./bin
```

test:
```bash
~/tools/gitjacker -h
```

We will use _Gitjacker_ to identify misconfigured Git repositories

## _pwncat_

_pwncat_ is a Python-based command-and-control library for capturing and interacting with remote shells. Once _pwncat_ receives a shell connection from a remote compromised host, it acts as an exploitation platform from which commands can be sent and attacks can be launched.

```bash
pip3 install pwncat-cs
```

We will use _pwncat_ for penetration-testing purposes

## _LinEnum_

_LinEnum_ is a bash script for enumerating local information on a Linux host

```bash
cd ~/tools
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod +x LinEnum.sh
```

We will use _LinEnum_ to enumerate systems for misconfigurations

## _unix-privesc-check_

The _unix-privesc-check_ shell scrip collects information from a host in an attempt to find misconfigurations and ways to escalate privileges. The script is written to support many flavors of Linux and Unix systems and does not require any dependencies, which makes it convenient to run

By default, the script comes bundled with Kali, and you should find it in `/usr/bin/unix-privesc-check`
Optionally, you can create a copy of it in the tools directory for easier access, should you need to copy it later to any of the lab’s machines: `cp /usr/bin/unix-privesc-check ~/tools`

If the script isn’t available on your Kali machine, you can download it directly from APT:
```bash
sudo apt-get install unix-privesc-check -y
```

We will use _unix-privesc-check_ to identify privilege escalation opportunities

## Assigning Aliases to Hacking Tools
Tools that are installed through third-party repositories such as GitHub sometimes won’t have setup files that make running them easier. We can assign these tools bash aliases as shorthand references so that we won’t need to enter the full directory path every time we run them.

Run these if you use `bash` shell:
```bash
echo "alias rustscan='docker run --network=host -it --rm --name rustscan rustscan/rustscan:2.1.1'" >> "/home/kali/.bashrc"

echo "alias gitjacker='/home/kali/tools/gitjacker'" >> ~/.bashrc
```
If you use `zsh` shell:
```bash
echo "alias rustscan='docker run --network=host -it --rm --name rustscan rustscan/rustscan:2.1.1'" >> "/home/kali/.zshrc"

echo "alias gitjacker='/home/kali/tools/gitjacker'" >> ~/.zshrc
```
RustScan and Gitjacker now have aliases.

At this point, you should have a fully functioning bash hacking lab. Now would be a good time to take a snapshot of your Kali virtual machine so you can restore it to this clean state. Taking snapshots regularly is a good idea, especially whenever you make significant configuration changes or deploy new tools to your virtual lab.

___
## References: 
- [Nuclei templates](https://github.com/projectdiscovery/nuclei-templates)
- [Linux Exploit Suggester 2](https://github.com/jondonas/linux-exploit-suggester-2.git)
- 

Created:: 2024-12-03 15:55