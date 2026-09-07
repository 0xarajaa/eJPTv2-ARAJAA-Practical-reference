#### Importing Nmap Scan Results into MSF
##### Save Nmap Results as XML

Run Nmap and save the output in XML format:

```bash
nmap -sV 10.4.24.205 -oX scan.xml
```

Meaning:

```text
-sV        → detect service versions
-oX        → save output as XML
scan.xml   → XML file to import into Metasploit
```

XML is used because Metasploit can parse it and add the results to its database.

---

##### Start the Database

Before importing results, the Metasploit database should be running.

Start PostgreSQL:

```bash
service postgresql start
```

Then start Metasploit:

```bash
msfconsole
```

Check database status inside Metasploit:

```bash
db_status
```

If the database is connected, Metasploit can store hosts, services, and scan results.

---

##### Workspaces

Workspaces help organize results inside Metasploit.

Show current workspaces:

```bash
workspace
```

Create a new workspace:

```bash
workspace -a nmap_scan
```

Switch to a workspace:

```bash
workspace nmap_scan
```

Delete a workspace:

```bash
workspace -d nmap_scan
```

Current workspace idea:

```text
Each workspace keeps its own hosts, services, notes, and scan results.
Useful when working on different labs or targets.
```

---

##### Import Nmap XML Into Metasploit

After creating or selecting the correct workspace, import the XML file:

```bash
db_import scan.xml
```

This imports the Nmap results into the Metasploit database.

After import, we can view discovered hosts:

```bash
hosts
```

View discovered services:

```bash
services
```

Example:

```text
hosts     → shows discovered IP addresses
services  → shows open ports and detected services
```

---

##### Running Nmap Inside Metasploit

Instead of running Nmap outside and importing XML manually, we can run Nmap directly inside Metasploit using:

```bash
db_nmap
```

Example:

```bash
db_nmap -sV 10.4.24.205
```

This runs Nmap and automatically saves the results into the current Metasploit workspace.

---
##### Port Scanning with Auxiliary Modules
Auxiliary modules in Metasploit are used for scanning, enumeration, validation, and information gathering.

They are not exploit modules by default.  
As penetration testers, we use them after discovery to check ports, services, and protocols directly from inside `msfconsole`.

---

##### Why We Use Them

They help us:

```text
Scan ports from Metasploit
Validate Nmap results
Enumerate services
Keep results inside the Metasploit database
Work inside the selected workspace
```

Nmap is usually better for full scanning.  
Auxiliary modules are useful for focused checks inside Metasploit.

---

##### Search for Port Scanner Modules

Inside Metasploit:

```bash
search portscan
```

Common modules:

```text
auxiliary/scanner/portscan/tcp
auxiliary/scanner/portscan/syn
auxiliary/scanner/portscan/ack
```

---
##### Basic Usage

Select a module:

```bash
use auxiliary/scanner/portscan/tcp
```

Show required options:

```bash
show options
```

Set the target:

```bash
set RHOSTS 10.4.24.205
```

Set ports:

```bash
set PORTS 21,22,80,443,445,3389
```

Set threads:

```bash
set THREADS 10
```

Run:

```bash
run
```

or:

```bash
exploit
```

---
##### Important Options

```text
RHOSTS   → target IP, range, or subnet
PORTS    → ports to scan
THREADS  → parallel scan speed
```

Examples:

```bash
set RHOSTS 10.4.24.0/24
```

```bash
set PORTS 1-1000
```

```bash
set PORTS 80,445,3389
```

---

##### Common Portscan Modules

TCP scanner:

```bash
use auxiliary/scanner/portscan/tcp
```

SYN scanner:

```bash
use auxiliary/scanner/portscan/syn
```

ACK scanner:

```bash
use auxiliary/scanner/portscan/ack
```

`tcp` checks TCP ports.  
`syn` performs SYN-based scanning.  
`ack` is mainly useful for checking filtering/firewall behavior, not for confirming open ports.

---
##### Checking Results
If the database is running, Metasploit can store and show results.

Show discovered hosts:

```bash
hosts
```

Show discovered services:

```bash
services
```

---
