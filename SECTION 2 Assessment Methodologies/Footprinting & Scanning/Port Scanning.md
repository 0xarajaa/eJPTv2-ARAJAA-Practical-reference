##### Port Scanning

Port scanning is used after host discovery to identify which ports are open on a live host.

In penetration testing, open ports help us understand what services are running and what should be enumerated next.

Basic flow:

```text
Host discovery
↓
Port scanning
↓
Service detection
↓
Enumeration
↓
Exploitation planning
```

---

##### `-Pn`

`-Pn` tells Nmap to skip host discovery and treat the target as alive.

```bash
nmap -Pn 10.4.24.205
```

Practical Insight:

Use `-Pn` when the host does not respond to ping, but you still believe it is alive.

This is useful because ICMP or discovery probes may be blocked by a firewall.

Meaning:

```text
-Pn → do not ping the host, scan it directly
```

---

##### Full Port Scan `-p-`

`-p-` scans all TCP ports from `1` to `65535`.

```bash
nmap -Pn -p- 10.4.24.205
```

With timing:

```bash
nmap -T4 -Pn -p- 10.4.24.205
```

Meaning:

```text
-p- → scan all TCP ports
```

Practical Insight:

This is useful when we do not want to miss services running on non-default ports.

Example:

```text
HTTP may run on 80, 8080, 8000, 8888
SSH may run on 22 or another custom port
```

---

##### Specific Port Scan `-p`

We can scan one specific port:

```bash
nmap -Pn -p80 10.4.24.205
```

Or multiple specific ports:

```bash
nmap -Pn -p21,22,80,443,445 10.4.24.205
```

Or a port range:

```bash
nmap -Pn -p1-1000 10.4.24.205
```

Meaning:

```text
-p80       → scan port 80 only
-p21,22,80 → scan selected ports
-p1-1000   → scan ports from 1 to 1000
```

Practical Insight:

Specific ports are faster and cleaner when we know what we want to check.

---

##### Fast Scan `-F`

`-F` performs a fast scan against fewer common ports.

```bash
nmap -Pn -F 10.10.23.4
```

Meaning:

```text
-F → fast scan common ports only
```

Practical Insight:

Use `-F` when you want a quick first look.

Limitation:

It may miss services running on uncommon ports.

---

##### Timing `-T4`

`-T4` makes the scan faster.

```bash
nmap -T4 -Pn -p- 10.4.24.205
```

Meaning:

```text
-T4 → faster timing
```


---

##### SYN Scan `-sS`

`-sS` is a TCP SYN scan.

```bash
nmap -Pn -sS -F 10.10.23.4
```

With selected ports:

```bash
nmap -Pn -sS -p21,22,80,443,445 10.4.24.205
```

Meaning:

```text
-sS → TCP SYN scan
```

How it works:

```text
Nmap sends SYN
If target replies SYN/ACK → port is open
Nmap sends RST instead of completing the handshake
```

Practical Insight:

This is often called a “stealth scan”, but that does not mean invisible.

It is “stealthier” than a full TCP connection because it does not complete the full TCP handshake.

Limitation:

Modern firewalls, IDS, IPS, and logs can still detect SYN scans.

So:

```text
Stealth scan ≠ invisible scan
```

---

##### TCP Connect Scan `-sT`

`-sT` performs a full TCP connection scan.

```bash
nmap -Pn -sT -p80 10.4.24.205
```

Meaning:

```text
-sT → full TCP connect scan
```

How it works:

```text
SYN
SYN/ACK
ACK
```

It completes the full TCP handshake.

Practical Insight:

`-sT` is noisier than `-sS` because it fully connects to the service.

It is usually used when SYN scan is not available or when the user does not have raw socket privileges.

Practical takeaway:

```text
-sS → preferred when possible
-sT → fallback when SYN scan is not available
```

---

##### Port States

Nmap may show different port states.

###### Open

```text
open
```

The port is accepting connections.

Meaning:

```text
A service is likely running on this port.
```

Example:

```text
80/tcp open http
445/tcp open microsoft-ds
```

##### Firewall Interpretation

A simple way to think about results:

```text
open     → service is responding
closed   → host replied, port is closed
filtered → firewall/filtering may be blocking the probe
open|filtered → common with UDP, unclear result
```

Practical Insight:

If many ports show `filtered`, there is likely a firewall or filtering device.

If ports show `closed`, the host is responding clearly, and the probe is not being silently dropped.

---

##### UDP Scan `-sU`

`-sU` scans UDP ports.

```bash
nmap -Pn -sU -p53,137,138,139 10.4.24.205
```

Meaning:

```text
-sU → UDP scan
-p53,137,138,139 → scan selected UDP ports
```

Common UDP ports:

```text
53   DNS
67   DHCP
69   TFTP
123  NTP
137  NetBIOS Name Service
138  NetBIOS Datagram Service
161  SNMP
```

Practical Insight:

UDP scanning is important but slower and less clear than TCP scanning.

Many UDP services do not respond unless the request is valid.

So Nmap may show:

```text
open|filtered
```

This means the port may be open, or packets may be filtered.

`-sU` scans UDP ports.

---
##### Service Version & OS Detection

Before scanning, we need to know the network we are connected to.

On Linux, we can check our IP address using:

```bash
ifconfig
```

This command can show us the range of the network we on

```bash
ip a s
```

Example:

```text
inet 192.168.1.25/24
```

This tells us that our machine is inside the `192.168.1.0/24` network.

Practical Insight:

Before scanning targets, we first understand our own network range.  
Then we perform host discovery to find live hosts inside that range.

---

##### Service Version Detection `-sV`

`-sV` is used to detect service names and versions.

```bash
nmap -sV 192.168.1.50
```

Meaning:

```text
-sV → detect service version
```

Example result:

```text
22/tcp open  ssh     OpenSSH 8.2p1
80/tcp open  http    Apache httpd 2.4.49
445/tcp open smb     Samba smbd 4.x
```

Practical Insight:

Open ports alone are not enough.

Knowing the service version is important because vulnerabilities are often version-specific.

##### OS Detection `-O`

`-O` is used to detect or guess the operating system of the target.

```bash
nmap -O 192.168.1.50
```

Meaning:

```text
-O → OS detection
```

Nmap tries to identify the operating system by analyzing network responses and TCP/IP behavior.

Example result:

```text
OS details: Linux 4.x
OS details: Microsoft Windows 10
OS details: Windows Server 2016
```

Practical Insight:

OS detection helps us understand the target environment.

For example:

```text
Linux target  → check SSH, Apache, Samba, cron, Linux privilege escalation paths
Windows target → check SMB, RDP, WinRM, Windows services, AD-related paths
```

---

##### Aggressive OS Guessing `--osscan-guess`

`--osscan-guess` tells Nmap to guess the OS more aggressively when it is not fully sure.

```bash
nmap -O --osscan-guess 192.168.1.50
```

Meaning:

```text
--osscan-guess → make a more aggressive OS guess
```

Practical Insight:

This can be useful when Nmap cannot confidently identify the OS.

Limitation:

The result may be less accurate because it is a guess.

So we treat it as a clue, not confirmed truth.

---

##### Version Intensity `--version-intensity`

`--version-intensity` controls how aggressively Nmap probes services for version detection.

Example:

```bash
nmap -sV --version-intensity 8 192.168.1.50
```

Meaning:

```text
--version-intensity 8 → use stronger service version probing
```

Intensity levels range from:

```text
0 to 9
```

Practical Insight:

Higher intensity may detect more accurate versions, but it can take longer and send more probes.

Common usage:

```text
Low intensity  → faster, less detail
High intensity → slower, more detail
```

---

##### Nmap Scripting Engine (NSE)

##### NSE

NSE stands for **Nmap Scripting Engine**.

It allows Nmap to run scripts against services to gather extra information or perform specific checks during enumeration.

Examples:

```text
HTTP enumeration
SMB enumeration
FTP checks
SSL checks
DNS checks
MongoDB checks
Vulnerability checks
```

---

##### NSE Scripts Location

Nmap scripts are stored in:

```bash
/usr/share/nmap/scripts/
```

List all scripts:

```bash
ls -al /usr/share/nmap/scripts/
```

Search for scripts related to a service:

```bash
ls -al /usr/share/nmap/scripts/ | grep -e "http"
```

Example:

```bash
ls -al /usr/share/nmap/scripts/ | grep -e "mongodb"
```

---

##### Script Help

To understand what a script does:

```bash
nmap --script-help=mongodb-info
```

This shows the script description, category, usage, and any supported arguments.

---

##### Default Scripts

Run default NSE scripts:

```bash
nmap -sC 10.4.24.205
```

Equivalent:

```bash
nmap --script=default 10.4.24.205
```

Common combination:

```bash
nmap -sC -sV 10.4.24.205
```

Meaning:

```text
-sC → run default scripts
-sV → detect service versions
```

---

##### Run Specific Script

Run a specific script:

```bash
nmap --script=mongodb-info 10.4.24.205
```

Against a specific port:

```bash
nmap -p27017 --script=mongodb-info 10.4.24.205
```

---

##### Choose Script Based on Service

```text
HTTP open    → http-* scripts
SMB open     → smb-* scripts
FTP open     → ftp-* scripts
DNS open     → dns-* scripts
MongoDB open → mongodb-* scripts
SSL open     → ssl-* scripts
```

Examples:

```bash
nmap -p80 --script=http-title 10.4.24.205
```

```bash
nmap -p445 --script=smb-os-discovery 10.4.24.205
```

```bash
nmap -p27017 --script=mongodb-info 10.4.24.205
```

---

##### Script Categories

Common NSE categories:

```text
default    → default scripts
safe       → safer checks
discovery  → information gathering
version    → version detection support
vuln       → vulnerability checks
auth       → authentication checks
brute      → brute-force checks
exploit    → exploit-related scripts
intrusive  → aggressive scripts
dos        → denial-of-service scripts
external   → scripts that contact external services
```

Run a category:

```bash
nmap --script=safe 10.4.24.205
```

```bash
nmap --script=discovery 10.4.24.205
```

```bash
nmap --script=vuln 10.4.24.205
```

Be careful with:

```text
intrusive
brute
exploit
dos
```

These can be noisy or disruptive.

---

##### OS Information Through NSE

Some NSE scripts can reveal OS-related information from services.

Example:

```bash
nmap -p445 --script=smb-os-discovery 10.4.24.205
```

This may show:

```text
Hostname
Domain/workgroup
OS details
SMB information
System time
```

Difference:

```text
-O                → OS detection using TCP/IP fingerprinting
smb-os-discovery → OS info collected from SMB service
```

---

##### Summary

Key options:

```text
-sC                  → run default scripts
--script=default     → run default script category
--script=<name>      → run specific script
--script=<category>  → run script category
--script-help=<name> → show script help
```

