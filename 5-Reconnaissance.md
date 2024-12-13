Status: ongoing

Tags: #kali #lab #reconnaissance #hacking

Links: [4-Additional_Tools](4-Additional_Tools.md) [README](README.md) [6-VulnScan & Fuzzing](6-VulnScan_&_Fuzzing.md)
___

# Reconnaissance
**Every hacking engagement starts with some form of information gathering. We’ll perform reconnaissance on targets by writing bash scripts to run various hacking tools. You’ll learn how to use bash to automate tasks and chain multiple tools into a single workflow**

---
## A) Creating Reusable Target Lists
A _scope_ is a list of systems or resources you’re allowed to target. In penetration testing or bug-hunting engagements, the target company might provide you with various types of scopes:
- Individual IP addresses, such as 172.16.10.1 and 172.16.10.2
- Networks, such as 172.16.10.0/24 and 172.16.10.1–172.16.10.254
- Individual domain names, such as _lab​.example​.com_
- A parent domain name and all its subdomains, such as _​*.example​.com_

### IP Addresses
Imagine that you need to create a file containing a list of IP addresses from 172.16.10.1 to 172.16.10.254. While you could write all 254 addresses by hand, this would be time-consuming. Let’s use bash to automate the job! We’ll consider three strategies: 

1. using the _seq_ command in a _for_ loop:
 ```bash
 #!/usr/bin/env bash
 for ip in $(seq 1 254); do
	 echo "172.16.10.${ip}" >> 172_16_10_host.txt
done
```

2. using brace expansion with _echo_:
`echo 10.1.0.{1..254} | sed 's/ /\n/g' >> 10_1_0_host.txt`


3. using brace expansion with _printf_:
`printf "10.1.0.%d\n" {1..254} >> 10_1_0_host.txt`

### Possible Subdomains
Say you’re performing a penetration test against a company with the parent domain example​.com. In this engagement, you’re not restricted to any specific IP address or domain name, which means that any asset you find on this parent domain during the information-gathering stage is considered in scope

How can we generate a list of possible subdomains for a target? Bash makes this easy. First, we’ll need a list of common subdomains. You can find such a list built into Kali at `/usr/share/wordlists/amass/subdomains-top1mil​-110000.txt` or `/usr/share/wordlists/amass/bitquark_subdomains_top100K.txt`

To make our practice easy and not time consuming, we use `subdomains-100.txt`.
You can find file in materials folder.

The file contains one subdomain per line without an associated parent domain. You’ll have to merge each subdomain with the target’s parent domain to form a fully qualified domain name:
  
```bash
#!/usr/bin/env bash
DOMAIN="${1}"
FILE="${2}"

while read -r subdomain; do
	echo "${subdomain}.${DOMAIN}"
done < "${FILE}"
```
Usage:
`./subdomain-merger.sh example.com <path/to/subdomain-100.txt>`

OR

`sed 's/$/.example.com/g' subdomains-100.txt`

---
## B) Host Discovery
When testing a range of addresses, one of the first things you’ll likely want to do is find out information about them. Do they have any open ports? What services are behind those ports, and are they vulnerable to any security flaws?

### _ping_
At its most basic form, the ping command takes one argument: a target IP address or domain name: `ping 172.16.10.10`

So, what about multiple hosts?
```bash
#!/usr/bin/env bash
FILE="${1}"

while read -r host; do
	if ping -c 1 -W 1 -w 1 "${host}" &> /dev/null; then
		echo "${host} is up"
	fi
done < "${FILE}" 
```

The caveat to this host-discovery approach is that certain hosts, especially hardened ones, might not reply to ping commands at all. So, if we rely solely on this method for discovery, we might miss out on live hosts on the network.

Also note that commands that run forever by default, such as ping, could pose a challenge when integrated into a bash script. In this example, we’ve explicitly set a few special flags to ensure that our bash script won’t hang when it executes ping. This is why it’s important to first test commands in the terminal before integrating them into your scripts. More often than not, tools have special options to ensure they don’t execute forever, such as timeout options.

For tools that don’t provide a timeout option, the timeout command allows you to run commands and exit after a certain amount of time has passed. You can prepend timeout to any Linux utility, passing it an interval (in the seconds, minutes, hours format)—for example, `timeout 5s ping 8.8.8.8` After the time has elapsed, the entire command exits.

### _nmap_
The _nmap_ port scanner has a special option called `-sn` that performs a _ping sweep_. This simple technique finds live hosts on a network by sending them a _ping_ command and waiting for a positive response (called a _ping response_).  Since many operating systems respond to _ping_ by default, this technique has proved valuable. 

The _ping sweep_ in _nmap_ will essentially make _nmap_ send **Internet Control Message Protocol** packets over the network to discover running hosts:
`nmap -sn 172.16.10.0/24`
Hmmm....  is out-put too confusing? use _grep_ and _awk_ combination:
`nmap -sn 172.16.10.0/24 | grep "Nmap scan" | awk -F'report for ' '{print $2}'` 

Using _nmap_’s built-in _ping sweep_ scan may be more useful than manually wrapping the ping utility with bash, because you don’t have to worry about checking for conditions such as whether the command was successful. Moreover, in penetration tests, you may drop an _nmap_ binary on more than one type of operating system, and the same syntax will work consistently whether the _ping_ utility exists or not.

### _arp-scan_
In PenTest, we perform testing remotely, or locally. Above methods are good for testing remotely. How about local tests? Is there any better ways?

The _arp-scan_ utility sends **Address Resolution Protocol** (ARP) packets to hosts on a network and displays any responses it gets back. **ARP** maps media access control (MAC) addresses, which are unique 12-digit hexadecimal addresses assigned to network devices, to the IP addresses on a network. Because **ARP** is a Layer 2 protocol in the **Open Systems Interconnection** (OSI) model, it is useful only when you are on a local network. 

At its most basic form, you can run it by executing the _arp-scan_ command and passing a single IP address as an argument:
`sudo arp-scan 172.16.10.10 -I br_public`
We also need to tell _arp-scan_ which network interface to send packets on, as Kali has a few network interfaces. To achieve this, we use the `-I` argument. The _br_public_ interface corresponds to the 172.16.10.0/24 network in the lab.

Also you can pass _arp-scan_ a **CIDR** range:
`sudo arp-scan 172.16.10.0/24 -I br_public`
or use hosts file we created little before 
`sudo arp-scan -f 172_16_10_host.txt -I br_public`

Out-put of scan consists of three fields:
- IP address
- MAC Address
- vendor details, identified by the first three octets of the MAC address

---

## C) Port Scanning
Once you’ve discovered hosts on the network, you can run a port scanner to find their open ports and the services they’re running. Let’s explore port scanning by using three tools: `Nmap`, `RustScan`, and `Netcat`

### _nmap_
_Nmap_ allows us to perform port scanning against single targets or multiple targets at the same time. And also it accepts IP addresses

When we provide _nmap_ with no special options on command line, it will use the following default settings:

- **Perform a SYN scan** 
  _Nmap_ will use a "synchronization (SYN) scan", AKA a _half-open scan_, to discover open ports on a target. This scan involves sending a SYN packet and waiting for a response.

- **Scan the top 1,000 ports** 
  _Nmap_ will scan only popular ports known to be frequently in use, such as TCP ports 21, 22, 80, and 443. It won’t scan the entire port range of 0–65,534, to conserve resources.

- **Scan TCP ports**
  _Nmap_ will scan only TCP ports, not "User Datagram Protocol (UDP)" ports

_Nmap_ can also read targets from a given file when passed the `-iL` option. The targets must be separated by newlines. Let's use the 172-16-10-host.txt file with _nmap_ to scan multiple targets:

```bash
nmap -sV -iL 172-16-10-host.txt
```

This scan may take some time to complete because of the use of the `-sV` option, which detects the version of services on each port. _Nmap_ returns a few IP addresses and their open ports, including their services and even information related to the operating system running on the host. 

But, there is a lot of information to handle for us, at least for now.
So, we need open ports use one of these techniques to filter output:

```bash
nmap -sV -iL 172-16-10-host.txt | grep open

# OR

nmap -sV -iL 172-16-10-host.txt --open
```

If you pay attention there is a IP (172.16.10.1) not related to our target lab which is Kali's own interface IP. you can just remove it from file to reduce confusion

### _RustScan_
_RustScan_ is becoming more popular in the bug-bounty and penetration testing spaces because of its speed and extensibility. The following rustscan command runs a port scan. The -a (address) argument accepts a single address or an address range:
```bash
rutscan -a 172.16.10.0/24
```

When you run _RustScan_, you may notice that the initial output contains banners, author credits, and additional information not directly related to the scan results. Use the `-g` (greppable) option to show only the scanning information. The following command uses the greppable output mode to scan 172.16.10.0/24 on the first 1,024 ports (also called privileged ports) with the `-r` (range) option:
```bash
rustscan -g -a 172.16.10.0/24 -r 1-1024
```

### _netcat_
You can also use _netcat_ for port scanning activities. People often use this tool when they want to check the state of a single port (such as whether it’s open or closed), but _netcat_ also enables you to scan multiple ports with a single command:
```bash
nc -zv 172.16.10.11 1-1024
```

We use `nc` with the `-z` flag (zero input/output, or I/O, mode, which won’t send any data) and the `-v` (verbose) flag, followed by the target IP and the port range separated by a hyphen (-).

---

## D) Banner Grabbing
_Banner grabbing_ is the process of extracting the information published by remote network services when a connection is established between two parties. Services often transmit these banners to “greet” clients, which can use the information provided in various ways, such as to ensure they’re connecting to the right target. Banners could also include a system admin message of the day or the service’s specific running version.

_Passive banner grabbing_ uses third-party websites to look up banner information. For example, websites such as [Shodan](https://shodan​.io), [ZoomEye](https://zoomeye​.org), and [Censys](https://censys​.io) perform scans to map the internet, grabbing banners, versions, website pages, and ports, then create an inventory using this data. We can use such websites to look up banner information without ever interacting with the target server ourselves.

_Active banner grabbing_ is the opposite; it establishes a connection to a server and interacts with it directly to receive its banner information.

Keep in mind that banners are generally free-form text fields, and they can be changed to mislead clients. For example, an Apache web server could present itself as another type of web server, such as nginx. Some organizations even create honeypot servers to lure threat actors (or penetration testers). Honeypots use deception technologies to masquerade as vulnerable servers, but their real purpose is to detect and analyze attacker activity. More often than not, however, banners transmit default settings that system administrators haven’t bothered to change.

### _active banner grabbing_
To demonstrate what active banner grabbing looks like, we’ll use the following Netcat command to connect to port 21 (FTP) running on IP address 172.16.10.11
```bash
nc 172.16.10.11 -v 21
```
In the output you can see, 172.16.10.11 is running the FTP server vsFTPd version 3.0.5. This information may change if the vsFTPd version gets upgraded or downgraded, or if the system adminstrator decides to disable banner advertisement completely in the FTP server configuration.

And as usual, we can automate this with bash

### Detecting HTTP Responses
You’ll often find the popular curl HTTP client on production systems. To perform banner grabbing on HTTP responses, we can use curl to send an HTTP request using the HEAD method. The HEAD method allows us to read response headers without fetching the entire response payload from the web server.

Web servers often advertise themselves by setting the Server HTTP response header to their name. Sometimes you may also encounter the running version advertised there. The following curl command sends an HTTP HEAD request to the p-web-01 machine (172.16.10.10:8081):
```bash
curl --head 172.16.10.10:8081
```
From response we can learn that "remote server is running a Python-based web framework named Werkzeug version 2.2.3, powered by Python version 3.11.1."

### Using Nmap scripts
Nmap is more than just a port scanner; we can transform it into a fullfledged vulnerability assessment tool. The Nmap Scripting Engine (NSE) allows penetration testers to write scripts in the Lua language to extend Nmap’s capabilities. Nmap comes preinstalled with some Lua scripts, as you can see in `/usr/share/nmap/scripts`

The _banner.nse_ script in the `/usr/share/nmap/scripts` folder allows you to grab the banners from many hosts simultaneously. The following bash command uses this script to perform a banner grab and service discovery (sV):
```bash
nmap -sV --script=banner.nse -iL 172-16-10-hosts.txt
```

When the banner-grabbing script finds a banner, the output line containing that banner will begin with a special character sequence (|\_). We can filter for this sequence to extract banner information, like so:
```bash
nmap -sV --script=banner.nse -iL 172-16-10-hosts.txt | grep "|_banner\||_http-server-header"
```

The blackice-icecap? value indicates that Nmap is unable to definitively discover the identity of the service. But if you look closely at the fingerprint-strings dump, you’ll see some HTTP​-related information that reveals the same response headers we found when banner grabbing manually using curl. Specifically, note the Werkzeug web server banner. With a bit of googling, you’ll find that this server runs on Flask, a Python-based web framework.


___
## Tasks:

1. Imagine that you want to be notified whenever a new host appears on the network. For example, maybe you want to know when new laptops or IT assets have connected. This could be useful if you’re testing a target in a different time zone, where device users might not be online when you are.
   
   Example is [here](https://github.com/void-inject/Learning-Bash-Notes-Projects/tree/Alerts)

2. Sorting your scan results into categories of interest is often useful. For example, you could dump results for each IP address into a dedicated file or organize the results based on the versions of the software found.
   
   Write a script that does the following: 
   - Runs _Nmap_ against hosts in a file
   - Uses bash to create individual files whose filenames are open ports
   - In each file, writes the IP address on which the corresponding port was open

   At the end, you should have a bunch of files, such as _port-22.txt_, _port-80.txt_, and _port-8080.txt_, and in each file, you should see one or more IP addresses at which that port was found to be open. This can be useful when you have a large number of target hosts and want to attack them in clusters by targeting specific protocols associated with given ports.
   
   Example is [here](https://github.com/void-inject/Learning-Bash-Notes-Projects/tree/ip-sorter)
## References:

Created:: 2024-12-04 19:21
