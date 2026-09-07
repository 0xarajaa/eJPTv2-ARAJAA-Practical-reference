##### Firewall Detection

During port scanning, Nmap port states can help us understand if there is filtering or a firewall in front of the target.

The important states here are:

```text
closed
filtered
unfiltered
```

---

##### Closed Ports

If Nmap shows a port as `closed`, this means the host replied, but no service is listening on that port.

Example:

```text
92 closed ports
```

Meaning:

```text
The target is reachable.
The port is not open.
The probe was not silently blocked.
```

When many ports show `closed`, this usually means there is no firewall silently filtering those probes.

Simple idea:

```text
closed = host replied, port is closed
```

---

##### Filtered Ports

If Nmap shows a port as `filtered`, this means Nmap did not get a clear response.

Possible reasons:

```text
Firewall
Packet filtering
ACL
IDS/IPS
Security device
```

Meaning:

```text
Nmap cannot confirm if the port is open or closed.
The traffic may be blocked or dropped.
```

Simple idea:

```text
filtered = possible firewall or filtering
```

---

##### ACK Scan `-sA`

`-sA` is used to check firewall filtering.

It does not tell us if a port is open.  
It helps us understand if the port is filtered or unfiltered.

Example:

```bash
nmap -sA -p445 10.4.27.83
```

Meaning:

```text
-sA   → TCP ACK scan
-p445 → test port 445
```

Possible results:

```text
unfiltered → the packet reached the host and got a response
filtered   → the packet was blocked or dropped
```

Important:

```text
-sA is not used to find open ports.
-sA is used to detect filtering.
```

---

##### Packet Fragmentation `-f`

`-f` tells Nmap to fragment packets into smaller pieces.

```bash
nmap -sS -f -p445 10.4.27.83
```

Meaning:

```text
-f → fragment packets
```

The idea is to make packets harder for some firewalls or IDS devices to inspect.

Example with service detection:

```bash
nmap -Pn -sS -sV -f -p445,3389 10.4.27.83
```

Meaning:

```text
-Pn        → treat host as alive
-sS        → TCP SYN scan
-sV        → service version detection
-f         → packet fragmentation
-p445,3389 → scan selected ports
```

Note:

```text
Fragmentation may help reduce detection in some cases,
but modern firewalls and IDS can still detect or reassemble fragmented traffic.
```

---

##### Decoy Scan `-D`

`-D` is used to add decoy IP addresses during the scan.

The goal is to make the scan appear as if it is coming from multiple IP addresses.

Example:

```bash
nmap -Pn -sS -sV -p445,3389 -D 10.10.23.1,10.10.23.2 10.4.27.83
```

Meaning:

```text
-D 10.10.23.1,10.10.23.2 → use decoy IPs
10.4.27.83               → real target
```

The target may see traffic from multiple IPs, making it harder to immediately identify the real scanner.

---

##### Using the Gateway as a Decoy

In an internal network, the gateway is often the `.1` address.

Example:

```text
Attacker network: 10.10.23.0/24
Gateway:          10.10.23.1
```

So we can use the gateway IP as one of the decoys:

```bash
nmap -Pn -sS -sV -p445,3389 -D 10.10.23.1,10.10.23.2 10.4.27.83
```

In the example from the lab:

```bash
nmap -Pn -sS -sV -p445,3389 -f --data-length 200 -D 10.10.23.1,10.10.23.2 10.4.27.83
```

Explanation:

```text
-Pn                 → skip host discovery
-sS                 → SYN scan
-sV                 → detect service versions
-p445,3389          → scan SMB and RDP
-f                  → fragment packets
--data-length 200   → add extra random data to packets
-D                  → use decoy IPs
10.4.27.83          → target IP
```

---

##### `--data-length`

`--data-length` adds extra random data to sent packets.

Example:

```bash
nmap --data-length 200 10.4.27.83
```

Meaning:

```text
--data-length 200 → add 200 bytes of random data
```

This changes the packet size and may help avoid simple signature-based detection.

---
##### Nmap Output Formats
Nmap can save scan results in different output formats.

This is useful because sometimes we want to:

```
Read results normally
Search results quickly
Import results into tools
Save all formats at once
```

---

##### Normal Output `-oN`

`-oN` saves the scan result in normal readable format.

```
nmap -sV 10.4.24.205 -oN scan.txt
```

Meaning:

```
-oN → normal output
```

Use it when you want a clean report that is easy to read manually.

Example file:

```
scan.txt
```

---

##### Grepable Output `-oG`

`-oG` saves the result in grepable format.

```
nmap -sV 10.4.24.205 -oG scan.gnmap
```

Meaning:

```
-oG → grepable output
```

Use it when you want to filter results quickly with tools like `grep`, `awk`, or `cut`.

Example:

```
grep "open" scan.gnmap
```

Good for quickly extracting open ports or live hosts.

---

##### XML Output `-oX`

`-oX` saves the scan result in XML format.

```
nmap -sV 10.4.24.205 -oX scan.xml
```

Meaning:

```
-oX → XML output
```

XML is useful when another tool needs to read the scan results.

Metasploit can import Nmap XML results.

Example inside Metasploit:

```
db_import scan.xml
```

This lets Metasploit store discovered hosts, ports, and services in its database.

---

##### All Formats `-oA`

`-oA` saves the output in all main formats at the same time.

```
nmap -sV 10.4.24.205 -oA scan_results
```

Meaning:

```
-oA → output all formats
```

This creates:

```
scan_results.nmap   → normal output
scan_results.gnmap  → grepable output
scan_results.xml    → XML output
```

This is usually the best option when we want to keep results organized.

---
