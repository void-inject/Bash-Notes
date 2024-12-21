[5-Reconnaissance](5-Reconnaissance.md) [README](Learning-Bash-Notes-Projects/README.md) 

Tags: #bash #hacking #kali #lab #fuzzing #nikto #cross_site_scripting #clickjacking #directory_indexing 

___

# VULNERABILITY SCANNING & FUZZING

## scanning websites with _nikto_

_Nikto_ performs banner grabbing and runs a few basic checks to determine if the web server uses security headers to mitigate known web vulnerabilities; these vulnerabilities include _cross-site scripting_ (XSS), which is a client-side injection vulnerability targeting web browsers, and UI redressing (also known as _clickjacking_), a vulnerability that lets attackers use decoy layers in a web page to hijack user clicks. The security headers indicate to browsers what to do when loading certain resources and opening URLs, protecting the user from falling victim to an attack.

After performing these security checks, _Nikto_ also sends requests to possible endpoints on the server by using its built-in wordlist of common paths. The requests can discover interesting endpoints that could be useful for penetration testers

We’ll run a Nikto scan against the web ports we found to be open on the three target IP addresses. Open a terminal and run the following commands one at a time so you can dissect the output for each IP address:
```bash
nikto -host 172.16.10.10 -port 8081
nikto -host 172.16.10.11 -port 80
nikto -host 172.16.10.12 -port 80
```

The output for 172.16.10.10 on port 8081 shouldn’t yield much interesting information about discovered endpoints, but it should indicate that the server doesn’t seem to be hardened, as it doesn’t use security headers:
```bash
+ Server: Werkzeug/2.2.3 Python/3.11.1
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user
agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user
agent to render the content of the site in a different fashion to the MIME
type
---------
+ Allowed HTTP Methods: OPTIONS, GET, HEAD
+ 7891 requests: 0 error(s) and 4 item(s) reported on remote host
```
Nikto was able to perform a banner grab of the server, as indicated by the line that starts with the word Server. It then listed a few missing security headers. These are useful pieces of information but not enough to take over a server just yet.

The IP address 172.16.10.11 on port 80 should give you a similar result, though _Nikto_ also discovered a new endpoint, `/backup`, and that directory indexing mode is enabled:
```bash
+ Server: Apache/2.4.55 (Ubuntu)
---------
+ OSVDB-3268: /backup/: Directory indexing found.
+ OSVDB-3092: /backup/: This might be interesting...
```
Directory _indexing_ is a server-side setting that, instead of a web page, lists files located at certain web paths. When enabled, the directory indexing setting lists the content of a directory when an index file is missing (such as index​.html or index​.php). Directory indexing is interesting to find because it could highlight sensitive files in an application, such as configuration files with connection strings, local database files (such as SQLite files), and other environmental files. Open the browser and search `http://172​.16​.10​.11​/ backup` to see the content of this endpoint.

Directory indexing lets you view files in the browser. You can click directories to open them, click files to download them, and so on. On the web page, you should identify two folders: acme-hyper branding and acme-impact-alliance. The acme-hyper-branding folder appears to contain a file named app.py. Download it to Kali by clicking it so it’s available for later inspection.

### Building a Directory Indexing Scanner
What if we wanted to run a scan against a list of URLs to check whether they enable directory indexing, then download all the files they serve?
```bash
#!/usr/bin/env bash

FILE="${1}"
OUTPUT_FOLDER="${2}"
if [[ ! -s "${FILE}" ]]; then
	echo "You must provide a non-empty hosts file as an argument."
	exit 1
fi
if [[ -z "${OUTPUT_FOLDER}" ]]; then
	OUTPUT_FOLDER="data"
fi
while read -r line; do
	url=$(echo "${line}" | xargs)
	if [[ -n "${url}" ]]; then
		echo "Testing ${url} for Directory indexing..."
		if curl -L -s "${url}" | grep -q -e "Index of /" -e "[PARENTDIR]"; then
			echo -e "\t -!- Found Directory Indexing page at ${url}"
			echo -e "\t -!- Downloading to the \"${OUTPUT_FOLDER}\" folder..."
			mkdir -p "${OUTPUT_FOLDER}"
			wget -q -r -np -R "index​.html*" "${url}" -P "${OUTPUT_FOLDER}"
		fi
	fi
done < <(cat "${FILE}")
```


___
