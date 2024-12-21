[3-Setting_Up](3-Setting_Up.md) [README](Bash-Notes/README.md) [5-Reconnaissance](5-Reconnaissance.md)

Tags: #linux #tools #kali #hacking #shell-scripting #tools #reconnaissance #vulnerability_scanning #whatweb #rustscan #nuclei #dirsearch #linuxexploitsuggester2 #gitjacker #pwncat #linenum #unixprivesccheck

---

# Additional Tools for Hacking

Kali Linux comes pre-installed with many useful tools, but some additional tools need to be manually installed to enhance your hacking lab environment.

To keep things organized, first, create a dedicated folder for storing these tools:

```bash
cd ~
mkdir tools
```

Move into the `tools` directory and start installing the tools listed below.

---

## 1. _WhatWeb_

_WhatWeb_ fingerprints websites and identifies their application stacks using a database of known application signatures. It can detect content management systems (CMS) like WordPress, web application firewalls (WAFs), and more.

Install _WhatWeb_:

```bash
sudo apt-get install whatweb -y
```

Example usage:

```bash
whatweb example.com
```

---

## 2. _RustScan_

_RustScan_ is a fast port scanner written in Rust. It can scan all 65,535 ports on a target within seconds.

Install the Docker version of _RustScan_:

```bash
sudo docker pull rustscan/rustscan:2.1.1
```

Example usage:

```bash
docker run --network=host -it --rm --name rustscan rustscan/rustscan:2.1.1 -a example.com
```

---

## 3. _Nuclei_

_Nuclei_ is a vulnerability scanner written in Go by **ProjectDiscovery**. It includes publicly available templates for detecting vulnerabilities.

Install _Nuclei_:

```bash
sudo apt-get install nuclei -y
```

First-time setup:

```bash
nuclei -update-templates
```

Example usage:

```bash
nuclei -u https://example.com -t ~/nuclei-templates/vulnerabilities/
```

---

## 4. _dirsearch_

_dirsearch_ is a multithreaded tool for discovering hidden directories and files on web servers.

Install _dirsearch_:

```bash
sudo apt-get install dirsearch -y
```

Example usage:

```bash
dirsearch -u https://example.com -e php,html,txt
```

---

## 5. _Linux Exploit Suggester 2_

_Linux Exploit Suggester 2_ identifies kernel exploits for vulnerable Linux systems.

Clone the repository:

```bash
cd ~/tools
git clone https://github.com/jondonas/linux-exploit-suggester-2.git
```

Run the tool:

```bash
cd linux-exploit-suggester-2
perl linux-exploit-suggester-2.pl
```

---

## 6. _Gitjacker_

_Gitjacker_ extracts sensitive data from improperly configured `.git` directories.

Install _Gitjacker_:

```bash
sudo apt-get install jq -y
cd ~
curl -s "https://raw.githubusercontent.com/liamg/gitjacker/master/scripts/install.sh" | bash
mv ./bin/gitjacker ~/tools/gitjacker
rmdir ./bin
```

Test the tool:

```bash
~/tools/gitjacker -h
```

---

## 7. _pwncat_

_pwncat_ is a Python-based exploitation platform for interacting with remote shells.

Install _pwncat_:

```bash
sudo apt install pwncat -y
```

Example usage:

```bash
pwncat -l 4444
```

---

## 8. _LinEnum_

_LinEnum_ is a Bash script for gathering system information and identifying potential privilege escalation vectors.

Download the script:

```bash
cd ~/tools
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod +x LinEnum.sh
```

Run _LinEnum_:

```bash
./LinEnum.sh
```

---

## 9. _unix-privesc-check_

_unix-privesc-check_ analyzes a Unix/Linux system for privilege escalation opportunities.

Verify the tool is installed (bundled with Kali):

```bash
/usr/bin/unix-privesc-check
```

Alternatively, install it manually:

```bash
sudo apt-get install unix-privesc-check -y
```

Optional: Copy it to the tools directory:

```bash
cp /usr/bin/unix-privesc-check ~/tools
```

---

## Assigning Aliases to Tools

For easier access, assign aliases to commonly used tools. Here are examples for both Bash and Zsh shells:

### For Bash:

```bash
echo "alias rustscan='docker run --network=host -it --rm --name rustscan rustscan/rustscan:2.1.1'" >> ~/.bashrc
echo "alias gitjacker='/home/vincenzo/tools/gitjacker'" >> ~/.bashrc
source ~/.bashrc
```

### For Zsh:

```bash
echo "alias rustscan='docker run --network=host -it --rm --name rustscan rustscan/rustscan:2.1.1'" >> ~/.zshrc
echo "alias gitjacker='/home/vincenzo/tools/gitjacker'" >> ~/.zshrc
source ~/.zshrc
```

---

## Taking Snapshots

At this point, your lab environment is ready for use. To ensure you can recover the system quickly, take a snapshot of your Kali virtual machine. Regular snapshots are recommended whenever significant changes are made to the environment.
