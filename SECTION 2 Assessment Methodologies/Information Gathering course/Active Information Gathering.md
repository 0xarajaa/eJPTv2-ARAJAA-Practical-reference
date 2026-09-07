#### DNS Zone Transfers
##### test

#### Host Discovery With Nmap
##### Nmap Host Discovery
A network reconnaissance technique used to identify live hosts on a network without performing a full port scan.  
  
In penetration testing, it helps discover active devices before deeper enumeration or scanning.

Basic host discovery:
```bash
nmap -sn target_ip
```

Example:
```bash
nmap -sn 192.168.1.0/24
```

Explanation:
- `-sn` performs host discovery only
- skips port scanning
- identifies live hosts on the network

Range Note:
Using:
```text
192.168.1.0/24
```

means scanning the entire subnet/network range.

The `.0/24` range typically includes:
```text
192.168.1.1 → 192.168.1.254
```

Used in real pentests to:
- identify active devices
- map local networks
- discover possible targets

---

##### Netdiscover
A network discovery tool in Kali Linux used to identify live hosts on local networks using ARP requests.  
  
Useful during internal network reconnaissance and local subnet discovery.

Install:
```bash
sudo apt install netdiscover
```

Example usage:
```bash
netdiscover -i eth0 -r 192.168.1.0/24
```

Explanation:
- `-i` specifies the network interface
- `-r` specifies the network range/subnet to scan

This scans the specified subnet using the selected network interface.

Practical Insight:
Netdiscover is commonly used inside local/internal networks because ARP discovery is:
- fast
- reliable
- useful for identifying devices on the same subnet

Real World Usage:
Used in internal pentests to:
- identify connected hosts
- discover unknown devices
- map internal infrastructure
- find gateways, printers, servers, and user machines

Limitation:
ARP discovery works mainly on local networks  
Does not work effectively across the internet

#### Port Scanning With Nmap

##### Nmap
A powerful network scanning and reconnaissance tool used to discover hosts services open ports operating systems and running technologies.  
  
In penetration testing it is one of the most important tools for enumeration and attack surface analysis.

GitHub:
https://github.com/nmap/nmap

---

Basic scan:
```bash
nmap target_ip
```

Used to perform a simple default scan on the target.

---

Service version detection + default scripts:
```bash
nmap -sV -sC target_ip
```

Explanation:
- `-sV` detects service versions
- `-sC` runs default NSE scripts

Used in real pentests to:
- identify software versions
- gather banners
- perform basic enumeration
- detect common misconfigurations

---

Stealth SYN scan + service detection + OS detection:
```bash
nmap -sS -sV -O target_ip
```

Explanation:
- `-sS` performs SYN stealth scan
- `-sV` detects service versions
- `-O` attempts OS detection

Used in real internal pentests for:
- stealthier scanning
- identifying services
- fingerprinting operating systems

---

Aggressive scan:
```bash
nmap -A target_ip
```

Explanation:
- `-A` enables:
  - version detection
  - OS detection
  - default scripts
  - traceroute

Used for:
- deeper enumeration
- attack surface profiling
- fast full reconnaissance

---

Scan specific ports:
```bash
nmap -p 80,443,8080 -sV target_ip
```

Explanation:
- `-p` specifies ports manually
- `-sV` detects versions on those ports

Used to:
- focus on important services
- reduce scan time
- validate known ports

---

Scan port range:
```bash
nmap -p1-1000 -sV target_ip
```

Explanation:
- scans ports from 1 to 1000

Used for:
- targeted enumeration
- faster scanning than full port scan

---

Scan all TCP ports:
```bash
nmap -p- -sV target_ip
```

Explanation:
- `-p-` scans all 65535 TCP ports

Used to discover:
- hidden services
- uncommon ports
- non-standard applications

---

Fast scan:
```bash
nmap -F target_ip
```

Explanation:
- `-F` scans fewer common ports only

Used for:
- quick reconnaissance
- rapid host discovery

---

UDP scan:
```bash
nmap -sU target_ip
```

Explanation:
- `-sU` scans UDP ports

Used to identify services such as:
- DNS
- SNMP
- DHCP
- VPN services

---

Skip host discovery:
```bash
nmap -Pn -sV target_ip
```

Explanation:
- `-Pn` skips ping discovery
- assumes host is alive

Used when:
- ICMP is blocked
- firewalls hide hosts
- ping requests fail

---

Verbose scan:
```bash
nmap -sS -sV -v target_ip
```

Explanation:
- `-v` enables verbose output

Used to:
- monitor scan progress
- display detailed scan information

---

Save normal output:
```bash
nmap -sS -sV -oN results.txt target_ip
```

Explanation:
- `-oN` saves normal text output

Used for:
- reporting
- documentation
- later analysis

---

Save XML output:
```bash
nmap -sS -sV -oX results.xml target_ip
```

Explanation:
- `-oX` saves XML formatted output

Used for:
- automation
- importing into other tools
- parsing scan results

---

Timing templates:
```bash
nmap -sS -T4 target_ip
```

Explanation:
- `-T` controls scan timing/speed

Timing levels:
- `T0` → very slow stealth
- `T1` → slow
- `T2` → polite
- `T3` → normal/default
- `T4` → fast
- `T5` → aggressive/very fast

Practical Insight:
- slower scans are stealthier
- faster scans are noisier
- `T4` commonly used in labs/internal pentests

---

Practical Workflow Examples:

Quick internal reconnaissance:
```bash
nmap -sS -sV -O -T4 target_ip
```

Full aggressive enumeration:
```bash
nmap -A -p- -T4 target_ip
```

Stealthier scan:
```bash
nmap -sS -Pn -T2 target_ip
```

UDP reconnaissance:
```bash
nmap -sU -T4 target_ip
```

---

Real World Usage:
Nmap is commonly used to:
- discover open ports
- identify running services
- detect operating systems
- map attack surface
- validate firewall exposure
- perform enumeration before deeper testing

---

Limitation:
Firewalls IDS IPS and filtering may hide ports or hosts  
UDP scans are slower and less reliable  
OS detection may not always be accurate