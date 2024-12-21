[4-Additional_Tools](4-Additional_Tools.md) [README](Bash-Notes/README.md) [6-VulnScan_&_Fuzzing](6-VulnScan_&_Fuzzing.md)

**Tags**: #bash #shell-scripting #linux #sed #ping #curl #reconnaissance #nmap #rustscan #netcat #arpscan #whatweb #host_discovery #network_discovery #port_discovery #service_detection #fingerprinting #banner_grabbing

---
# Reconnaissance in Penetration Testing

**Reconnaissance** is the first and crucial phase in any hacking engagement. This phase involves gathering information about the target network, systems, and infrastructure. By automating tasks with tools like Bash scripts and integrating various hacking utilities, we can gather valuable data, identify attack surfaces, and prepare for further exploitation phases.
## A) Creating Reusable Target Lists

Creating and managing a list of targets is essential for any reconnaissance process. This allows penetration testers to efficiently automate scanning across multiple hosts, domains, and subdomains.

### IP Addresses

For a typical engagement, you may receive a list of IP addresses that you are allowed to target. A common task is to create a file containing IP addresses within a given range. Here's how to do it:

#### Example 1: Using `seq` in a Bash loop

```bash
#!/usr/bin/env bash
for ip in $(seq 1 254); do
  echo "172.16.10.${ip}" >> 172_16_10_host.txt
done
```

This loop generates a list of IP addresses from `172.16.10.1` to `172.16.10.254` and saves it in `172_16_10_host.txt`.

#### Example 2: Using Brace Expansion

```bash
echo 172.16.10.{1..254} > 172_16_10_host.txt
```

This one-liner is a more efficient way to generate a list of IP addresses using brace expansion.

#### Example 3: Using `printf` for precise formatting

```bash
printf "172.16.10.%d\n" {1..254} >> 172_16_10_host.txt
```

This command creates the list with proper formatting, appending each IP on a new line in `172_16_10_host.txt`.

---

### Subdomain Enumeration

Subdomain enumeration helps in identifying potential attack surfaces on a parent domain. Here's how to create a list of subdomains:

#### Subdomain Merger Script

If you have a list of common subdomains (like `subdomains-100.txt`), you can merge them with a target domain:

```bash
#!/usr/bin/env bash
DOMAIN="${1}"
FILE="${2}"

while read -r subdomain; do
  echo "${subdomain}.${DOMAIN}"
done < "${FILE}"
```

Usage:

```bash
./subdomain-merger.sh example.com subdomains-100.txt
```

This script takes each subdomain from `subdomains-100.txt` and appends the target domain to it.

#### Alternative with `sed`

```bash
sed 's/$/.example.com/g' subdomains-100.txt
```

This command appends `.example.com` to each line in the `subdomains-100.txt` file, outputting the fully qualified domain names (FQDNs).

---

## B) Host Discovery

After identifying target IPs and domains, the next step is to discover active hosts, identify open ports, and gather information on running services.

### Using `ping` for Host Discovery

A simple way to check if a host is online is to use the `ping` command. Here's how you can automate the ping check for multiple hosts:

```bash
#!/usr/bin/env bash
FILE="${1}"

while read -r host; do
  if ping -c 1 -W 1 -w 1 "${host}" &> /dev/null; then
    echo "${host} is up"
  fi
done < "${FILE}"
```

This script will check if each host in the file is responsive to a ping.

---

### Using `nmap` for Host Discovery

`nmap` is a powerful tool for network discovery. The `-sn` option performs a "ping sweep," which helps to identify live hosts without scanning ports.

```bash
nmap -sn 172.16.10.0/24
```

To filter the output and only display live hosts:

```bash
nmap -sn 172.16.10.0/24 | grep "Nmap scan" | awk -F'report for ' '{print $2}'
```

This command shows only the IP addresses of live hosts.

---

### Using `arp-scan` for Local Host Discovery

If you're working on a local network and need to identify all active hosts, `arp-scan` is an excellent tool. It sends ARP packets to all devices on the network and reports back their IP and MAC addresses.

```bash
sudo arp-scan 172.16.10.0/24 -I br_public
```

Here, `br_public` specifies the network interface to use. You can also specify a file containing a list of IPs:

```bash
sudo arp-scan -f 172_16_10_host.txt -I br_public
```

---

### Analyzing Web Applications with `WhatWeb`

`WhatWeb` is a tool to fingerprint websites and identify technologies, such as web servers, frameworks, and software versions.

```bash
whatweb 172.16.10.10:8081
```

You can output the results in JSON format:

```bash
whatweb 172.16.10.10:8081 --log-json=/dev/stdout --quiet | jq
```

Then, use `jq` to parse the relevant information:

```bash
whatweb 172.16.10.10:8081 --log-json=/dev/stdout --quiet | jq '.[0].plugins.HTTPServer.string[0]'
```

This command extracts the web server's information from the JSON output.

---

## C) Port Scanning

Once live hosts are discovered, the next step is to scan their open ports. Here, we explore several tools to help with port scanning.

### Using `nmap` for Port Scanning

By default, `nmap` scans the top 1,000 most common ports. To scan all ports or use specific options:

```bash
nmap -sV -iL 172_16_10_host.txt
```

To scan only open ports:

```bash
nmap -sV -iL 172_16_10_host.txt --open
```

To identify services and versions on open ports, use `-sV`.

---

### Using `RustScan` for Fast Port Scanning

`RustScan` is a fast port scanner that can scan a range of ports quickly. You can scan a network range:

```bash
rustscan -g -a 172.16.10.0/24 -r 1-1024
```

This command scans the first 1,024 ports on all hosts in the network range.

---

### Using `Netcat` for Port Scanning

Netcat is a versatile tool that can also perform port scans. Here's a simple example for scanning ports 1–1024:

```bash
nc -zv 172.16.10.11 1-1024
```

The `-z` flag scans without sending data, and `-v` enables verbose output.

---

## D) Banner Grabbing

Banner grabbing helps in identifying service versions, which can be useful for vulnerability exploitation. This can be done either passively or actively.

### Active Banner Grabbing with `Netcat`

You can grab banners by connecting to services like FTP or HTTP using `Netcat`.

```bash
nc 172.16.10.11 21
```

This command connects to the FTP service on port 21 and retrieves the banner.

### Grabbing HTTP Headers with `curl`

For HTTP services, use `curl` to retrieve the headers.

```bash
curl --head 172.16.10.10:8081
```

This sends an HTTP HEAD request and retrieves the server banner, including details about the server version.

---

## Task: 1 

Imagine that you want to be notified whenever a new host appears on the network. For example, maybe you want to know when new laptops or IT assets have connected. This could be useful if you’re testing a target in a different time zone, where device users might not be online when you are.

### Example:

```bash
#!/env/bin/env bash

# Ensure both arguments are provided
if [ $# -ne 2 ]; then
    echo "Usage: $0 <network_range> <host_list_file>"
    exit 1
fi

# Read the network range and host list file from the command-line arguments
NETWORK=$1
HOST_LIST_FILE=$2

# Set file locations for storing the current scan results
CURRENT_SCAN_FILE="/tmp/current_scan.txt"

# Function to send a notification (can be customized)
send_notification() {
    local new_host=$1
    echo "New host detected: $new_host" | /
	    mail -s "New Host Alert" your_email@example.com
    # Or use notify-send for desktop notification
    # Example: notify-send "New host detected: $new_host"
}

# Run nmap scan to find live hosts in the specified network
nmap -sn $NETWORK -oG $CURRENT_SCAN_FILE

# Check if the host list file exists
if [ ! -f $HOST_LIST_FILE ]; then
    echo "Host list file not found!"
    exit 1
fi

# Function to check if a host is in the list
is_host_in_list() {
    local host=$1
    grep -q "$host" "$HOST_LIST_FILE"
}

# Read the current scan file and check for new hosts
echo "Scanning for new hosts..."
while read -r line; do
    # Extract the IP address or hostname from the nmap output
    IP_ADDRESS=$(echo $line | awk '{print $2}')
    
    # If the line contains a valid IP address and is not already in the list, send notification
    if [[ $IP_ADDRESS =~ ^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$ ]] /
			    && ! is_host_in_list "$IP_ADDRESS"; then
        
        # Send notification for the new host
        send_notification "$IP_ADDRESS"
    fi
done < <(grep "Host:" $CURRENT_SCAN_FILE)

echo "Scan completed."

```

## Taks: 2

Sorting your scan results into categories of interest is often useful. For example, you could dump results for each IP address into a dedicated file or organize the results based on the versions of the software found.

Write a script that does the following:
- Runs _Nmap_ against hosts in a file
- Uses bash to create individual files whose filenames are open ports
- In each file, writes the IP address on which the corresponding port was open

At the end, you should have a bunch of files, such as _port-22.txt_, _port-80.txt_, and _port-8080.txt_, and in each file, you should see one or more IP addresses at which that port was found to be open. This can be useful when you have a large number of target hosts and want to attack them in clusters by targeting specific protocols associated with given ports.

### Example:
```bash
#!/bin/bash

# Ensure the input file (host list) is provided
if [ $# -ne 1 ]; then
    echo "Usage: $0 <host_list_file>"
    exit 1
fi

# Read the host list file
HOST_LIST_FILE=$1

# Check if the host list file exists
if [ ! -f $HOST_LIST_FILE ]; then
    echo "Host list file not found!"
    exit 1
fi

# Create a directory to store results (if it doesn't exist)
RESULTS_DIR="nmap_results"
mkdir -p "$RESULTS_DIR"

# Run Nmap scan against hosts in the provided file
# -p- scans all ports (optional, can be customized)
# -oG saves the results in a greppable format
nmap -iL "$HOST_LIST_FILE" -p- -oG "$RESULTS_DIR/nmap_scan.txt"

# Process the results and categorize by open ports
echo "Processing results and categorizing by open ports..."

# Loop through the results and extract IPs and open ports
grep "open" "$RESULTS_DIR/nmap_scan.txt" | while read -r line; do
    # Extract the IP address and open port(s)
    IP_ADDRESS=$(echo "$line" | awk '{print $2}')
    OPEN_PORTS=$(echo "$line" | grep -oP '\d+/open' | cut -d'/' -f1)

    # For each open port, create a file and append the IP address
    for PORT in $OPEN_PORTS; do
        # Create or append to the file named after the open port
        PORT_FILE="$RESULTS_DIR/port-$PORT.txt"
        echo "$IP_ADDRESS" >> "$PORT_FILE"
    done
done

# Print the result directory and files created
echo "Scan results saved in '$RESULTS_DIR'."

```

