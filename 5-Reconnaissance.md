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

## C) Port Scanning

___
## Tasks:
1. Imagine that you want to be notified whenever a new host appears on the network. For example, maybe you want to know when new laptops or IT assets have connected. This could be useful if you’re testing a target in a different time zone, where device users might not be online when you are.
   Example is [here](https://github.com/void-inject/Learning-Bash-Notes-Projects/tree/Alerts)
## References:

Created:: 2024-12-04 19:21