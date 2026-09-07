##### Ping Sweeps
A host discovery technique used to identify live hosts inside a network by sending ICMP Echo Requests and checking which systems respond.

In penetration testing, ping sweeps help us quickly understand which IP addresses are active before moving to deeper port scanning or service enumeration.

##### ICMP
ICMP stands for **Internet Control Message Protocol**.

It is commonly used for network diagnostics and connectivity testing.  
The most common example is `ping`, which sends an **ICMP Echo Request** to a target and waits for an **ICMP Echo Reply**.

Basic ping test:

```bash
ping -c 4 192.168.1.10
```

Explanation:

```text
-c 4  → send only 4 packets
192.168.1.10 → target IP address
```

If the host replies, it means the system is reachable over ICMP.

---

##### Ping Sweep Concept

Instead of testing one IP address manually, a ping sweep tests a range of IP addresses to identify which hosts are alive.

Example network range:

```text
192.168.1.1 - 192.168.1.254
```

The goal is to discover live hosts before scanning them with tools like Nmap.

Practical flow:

```text
Find live hosts
↓
Identify active IP addresses
↓
Scan open ports
↓
Enumerate services
↓
Continue deeper testing
```

---

##### fping

`fping` is a faster ping sweep tool available in Kali Linux.  
It is useful when testing multiple hosts or an entire subnet.

Basic usage:

```bash
fping -a -g 192.168.1.0 192.168.1.255
```

Explanation:

```text
-a  → show only alive hosts
-g  → generate a range of IP addresses
```

Example output:

```text
192.168.1.1
192.168.1.10
192.168.1.25
```

This means these hosts responded to ICMP and are likely alive.

---

##### fping With CIDR Range

We can also scan a full subnet using CIDR notation:

```bash
fping -a -g 192.168.1.0/24
```

This checks all hosts in the `/24` range.

Example:

```text
192.168.1.0/24 = 192.168.1.1 to 192.168.1.254
```

---

##### Practical Insight

Ping sweeps are useful because they help reduce the target scope.

Instead of scanning every possible IP blindly, we first identify which hosts are likely alive, then focus our scanning on those systems.

Typical workflow:

```text
Ping sweep
↓
Live host list
↓
Port scan
↓
Service detection
↓
Vulnerability analysis
```

---

##### Important Limitation

No response does not always mean the host is down.

Sometimes the host is alive, but ICMP is blocked by:

```text
Windows Firewall
Host-based firewall
Network firewall
Security appliances
Router ACLs
IDS/IPS rules
```

For example, Windows systems often block ICMP Echo Requests by default or through firewall policies.

So if a host does not reply to ping, it may still be online.

---

##### Practical Note

If ICMP is blocked, we can use other discovery methods later, such as:

```text
TCP ping
ARP scan
Nmap host discovery
Port-based discovery
```

So the correct mindset is:

```text
Ping reply = host is likely alive
No ping reply = host may be down OR ICMP may be blocked
```

---

##### Summary

Ping sweeps are an early host discovery technique used to identify live systems in a network.  
They rely mainly on ICMP Echo Requests, but they are not always reliable because firewalls may block ICMP traffic.

Practical takeaway:

```text
Use ping sweeps for quick discovery,
but never assume a host is down only because it did not reply to ICMP.
```

##### Nmap Host Discovery

A host discovery technique used to identify live hosts in a network before performing deeper port scanning or service enumeration.

In penetration testing, it helps reduce the scope by finding which IP addresses are actually online before spending time scanning ports and services.

Basic flow:

```text
Host discovery
↓
Live hosts
↓
Port scanning
↓
Service enumeration
↓
Further testing
```

---

##### Nmap Help

Quick help menu:

```bash
nmap -h
```

Full Nmap manual:

```bash
man nmap
```

Practical Insight:

`nmap -h` is useful when we need a quick reminder of options.  
`man nmap` gives the full documentation and deeper explanation for scan types, timing, host discovery, and output options.

---

##### Basic Host Discovery With `-sn`

`-sn` is used for host discovery only.

It tells Nmap to check whether the host is alive without performing a full port scan.

```bash
nmap -sn 10.4.20.217
```

Meaning:

```text
-sn  → host discovery only / ping scan
```

Practical Insight:

This is usually one of the first steps before port scanning.

If the host is detected as up, we can continue with deeper scanning.  
If it does not respond, we should not immediately assume it is down because firewalls may block discovery probes.

---

##### Scanning a Full Subnet

To scan an entire subnet, we can use CIDR notation.

```bash
nmap -sn 10.4.20.0/24
```

Explanation:

```text
10.4.20.0/24 → scans the full 10.4.20.x network
```

Practical note:

When scanning a subnet, we usually place `0` at the end of the IP and use `/24`.

Example:

```text
Single host: 10.4.20.217
Full subnet: 10.4.20.0/24
```

---

##### Scanning Multiple IPs

We can scan multiple IP addresses in the same command.

```bash
nmap -sn 10.4.20.217 10.4.20.218
```

This checks both hosts at the same time.

We can also scan multiple separated targets if we already know the IPs we want to check.

---

##### Scanning Targets From a File

If we have a list of targets saved in a file, we can use `-iL`.

Example file:

```text
targets.txt
```

Example content:

```text
10.4.20.217
10.4.20.218
10.4.20.219
```

Run Nmap against the list:

```bash
nmap -sn -iL targets.txt
```

Meaning:

```text
-iL → read targets from a file
```

Practical Insight:

This is useful when we collect targets first, save them in a file, and then scan them in one organized run.

---

##### ICMP Echo Discovery `-PE`

`-PE` tells Nmap to use ICMP Echo Requests.

This is similar to normal ping behavior.

```bash
nmap -sn -PE 10.4.20.217
```

For a subnet:

```bash
nmap -sn -PE 10.4.20.0/24
```

Meaning:

```text
-PE → ICMP Echo Request discovery
```

Practical Insight:

ICMP is simple and useful, but it is often blocked by firewalls.

Limitation:

```text
No ICMP reply does not always mean the host is down.
The host may be alive, but ICMP may be blocked.
```

---

##### TCP SYN Discovery `-PS`

`-PS` tells Nmap to send TCP SYN probes to selected ports.

Example with one port:

```bash
nmap -sn -PS80 10.4.20.217
```

Example with specific ports:

```bash
nmap -sn -PS21,22,25,80,445,3389,8080 10.4.20.217
```

For a full subnet:

```bash
nmap -sn -PS21,22,25,80,445,3389,8080 10.4.20.0/24
```

Meaning:

```text
-PS → TCP SYN discovery
```

Practical Insight:

Even when using `-sn`, the ports after `-PS` are used only for host discovery probes.  
This is not a full port scan.

Why it is useful:

Some hosts block ICMP, but they may still respond to TCP SYN probes on common ports.

Common useful TCP ports:

```text
21    FTP
22    SSH
25    SMTP
80    HTTP
445   SMB
3389  RDP
8080  Alternative HTTP
```

---

##### TCP SYN Discovery With Port Range

We can also use a port range instead of specific ports.

```bash
nmap -sn -v -PS1-1000 -T4 10.4.20.217
```

For a subnet:

```bash
nmap -sn -v -PS1-1000 -T4 10.4.20.0/24
```

Meaning:

```text
-sn       → host discovery only
-v        → verbose output
-PS1-1000 → TCP SYN probes on ports 1 to 1000
-T4       → faster timing
```

Practical Insight:

This can be useful when we do not know which TCP ports may respond.  
However, using specific ports is often cleaner and faster when we already know common services to test.

---

##### Specific Ports Instead of Port Range

Instead of scanning a large range like:

```bash
nmap -sn -v -PS1-1000 -T4 10.4.20.0/24
```

We can use specific common ports:

```bash
nmap -sn -v -PS21,22,25,80,445,3389,8080 -T4 10.4.20.0/24
```

Practical Insight:

Specific ports are usually more controlled, faster, and cleaner.

Good practical ports:

```text
21,22,25,80,445,3389,8080
```

These ports are useful because they commonly appear in internal networks and may trigger a response from live hosts.

---

##### TCP ACK Discovery `-PA`

`-PA` tells Nmap to send TCP ACK probes.

```bash
nmap -sn -PA80 10.4.20.217
```

With multiple ports:

```bash
nmap -sn -PA80,443,445 10.4.20.0/24
```

Meaning:

```text
-PA → TCP ACK discovery
```

Practical Insight:

ACK discovery can help in some networks, but it may also be blocked by firewalls.

Limitation:

```text
ACK probes may be dropped by firewalls.
No response does not always mean the host is down.
```

---

##### UDP Discovery `-PU`

`-PU` tells Nmap to send UDP probes to selected UDP ports.

```bash
nmap -sn -PU137,138 10.4.20.217
```

For a subnet:

```bash
nmap -sn -PU137,138 10.4.20.0/24
```

Meaning:

```text
-PU      → UDP discovery
137,138  → NetBIOS UDP ports
```

Practical Insight:

UDP discovery can be useful in internal networks, especially when testing Windows-related services or legacy network environments.

Common UDP discovery ports:

```text
53   DNS
123  NTP
137  NetBIOS Name Service
138  NetBIOS Datagram Service
161  SNMP
```

Limitation:

UDP can be filtered or dropped silently, so results may not always be reliable.

---

##### Verbose Mode `-v`

`-v` enables verbose output.

```bash
nmap -sn -v 10.4.20.217
```

Meaning:

```text
-v → show more details during the scan
```

Practical Insight:

Verbose mode is useful while learning because it shows more information about what Nmap is doing.

---

##### Timing Template `-T4`

`-T4` makes Nmap run faster.

```bash
nmap -sn -T4 10.4.20.0/24
```

Meaning:

```text
-T4 → faster timing template
```

Practical Insight:

`-T4` is useful in labs and internal networks where the environment is stable.

Limitation:

Avoid using aggressive timing blindly on sensitive, unstable, or production networks.

---

##### Important Practical Command

A practical host discovery command combining TCP SYN and UDP discovery:

```bash
nmap -sn -v -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 10.4.20.217
```

For a full subnet:

```bash
nmap -sn -v -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 10.4.20.0/24
```

Explanation:

```text
-sn                          → host discovery only
-v                           → verbose output
-PS21,22,25,80,445,3389,8080 → TCP SYN probes on common TCP ports
-PU137,138                   → UDP probes on NetBIOS ports
-T4                          → faster timing
10.4.20.0/24                 → full subnet
```

Practical Insight:

This command is useful because it does not rely only on ICMP.

It combines:

```text
TCP SYN discovery
+
UDP discovery
+
verbose output
+
faster timing
```

This improves the chance of finding live hosts when ICMP is blocked.

---

##### Firewall Limitations

Host discovery can be affected by firewalls and filtering.

Examples:

```text
ICMP Echo Requests may be blocked.
TCP ACK probes may be dropped.
UDP probes may be filtered.
Windows Firewall may block ping replies.
Network firewalls may block discovery traffic.
```

Important mindset:

```text
No response does not always mean the host is down.
The host may be alive, but the discovery probe may be blocked.
```

---

##### Why SYN Discovery Is Often Effective

In many practical situations, TCP SYN discovery is more useful than relying only on ICMP.

Reason:

```text
Many networks block ICMP,
but hosts may still respond on common TCP ports.
```

Good practical command:

```bash
nmap -sn -v -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 10.4.20.0/24
```

Practical takeaway:

```text
ICMP is simple but often blocked.
ACK can also be filtered.
UDP may help in internal networks.
SYN discovery is often the most practical option in filtered environments.
```

---

##### Summary

Nmap host discovery is used to identify live hosts before deeper scanning.

Key options:

```text
-sn  → host discovery only
-PE  → ICMP Echo discovery
-PS  → TCP SYN discovery
-PA  → TCP ACK discovery
-PU  → UDP discovery
-v   → verbose output
-T4  → faster timing
-iL  → read targets from a file
```

Best practical workflow:

```text
Start with basic -sn
↓
If results are limited, try -PE, -PS, -PA, or -PU
↓
Use specific common ports or a focused range
↓
Identify live hosts
↓
Move to port scanning and service enumeration
```
