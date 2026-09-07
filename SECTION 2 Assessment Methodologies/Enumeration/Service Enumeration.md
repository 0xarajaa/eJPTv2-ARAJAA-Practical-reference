#### FTP Enumeration
FTP enumeration is used to gather information from an FTP service after discovering that port `21` is open.
##### Search in Metasploit
Inside Metasploit, we can search for FTP-related modules:

```bash
search ftp
```

To focus on scanner modules:

```bash
search type:auxiliary ftp
```

To search for FTP exploits:

```bash
search type:exploit ftp
```

Common idea:

```text
auxiliary → scanners, enumeration, login checks
exploit   → actual exploit modules
```

For enumeration, we usually start with auxiliary modules, not exploit modules.

---
##### Useful FTP Auxiliary Modules
Common FTP enumeration modules:

```text
auxiliary/scanner/ftp/ftp_version
auxiliary/scanner/ftp/anonymous
auxiliary/scanner/ftp/ftp_login
```

What they do:

```text
ftp_version → grabs FTP banner/version
anonymous   → checks if anonymous login is allowed
ftp_login   → tests credentials against FTP
```

---

##### FTP Version Module

Use the FTP version scanner:

```bash
use auxiliary/scanner/ftp/ftp_version
```

Show options:

```bash
show options
```

Set the target:

```bash
set RHOSTS <target>
```

Run:

```bash
run
```

This helps identify the FTP server banner and version.

---

##### Anonymous FTP Check

Anonymous FTP means the server allows login without a real user account.

Use:

```bash
use auxiliary/scanner/ftp/anonymous
```

Set target:

```bash
set RHOSTS <target>
```

Run:

```bash
run
```

If anonymous login is allowed, try logging in manually:

```bash
ftp <target>
```

Username:

```text
anonymous
```

Password can often be empty or any email-like value:

```text
anonymous
```

---
Connect manually:

```bash
ftp <target>
```

Useful FTP commands:

```text
ls      → list files
pwd     → show current directory
cd      → change directory
get     → download file
put     → upload file, if write is allowed
binary  → switch to binary mode
bye     → exit FTP
```

Example:

```text
ftp> ls
ftp> get file.txt
ftp> bye
```

Look for files such as:

```text
backup
config
passwords
users
readme
notes
.ssh
*.txt
*.bak
*.conf
```

---

##### Credential Testing

If credentials are provided or discovered during the lab, test them carefully.

Metasploit module:

```bash
use auxiliary/scanner/ftp/ftp_login
```

Important options:

```text
RHOSTS   → target IP or range
USERNAME → single username
PASSWORD → single password
USER_FILE → username list
PASS_FILE → password list
```

Set the username wordlist:

```
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
```

Set the password wordlist:

```
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

Example with one credential pair:

```bash
set RHOSTS <target>
set USERNAME <user>
set PASSWORD <password>
run
```
This should only be used in authorized labs or approved engagements.

---
#### SMB Enumeration
SMB enumeration is used to gather information from an SMB/Samba service after discovering that ports `139` or `445` are open.

##### Default SMB Ports
Main TCP ports:

```text
139 → SMB over NetBIOS
445 → SMB directly over TCP
```

Basic scan:

```bash
nmap demo.ine.local
```

NetBIOS also uses UDP:

```text
137 → NetBIOS Name Service
138 → NetBIOS Datagram Service
```

UDP scan:

```bash
nmap -sU --top-ports 25 demo.ine.local
```

---

##### SMB Service Detection
Check SMB service and version:

```bash
nmap -sV -p 445 demo.ine.local
```

This can help identify:

```text
SMB/Samba service
Version
Host information
Workgroup/domain information
```

---

##### SMB OS Discovery
Use the Nmap SMB discovery script:

```bash
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
```

It may reveal:

```text
Operating system
Samba version
Computer name
Workgroup/domain
System time
```

---

##### Search in Metasploit
Search for SMB modules:

```text
search smb
```

Scanner/enumeration modules:

```text
search type:auxiliary smb
```

Exploit modules:

```text
search type:exploit smb
```

Common idea:

```text
auxiliary → scanners, enumeration, login checks
exploit   → actual exploit modules
```

For SMB enumeration, start with auxiliary modules.

---

##### Useful SMB Auxiliary Modules
Common modules:

```text
auxiliary/scanner/smb/smb_version
auxiliary/scanner/smb/smb_enumshares
auxiliary/scanner/smb/smb_enumusers
auxiliary/scanner/smb/smb_login
```

What they do:

```text
smb_version    → detects SMB/Samba version and host information
smb_enumshares → enumerates SMB shares
smb_enumusers  → enumerates users
smb_login      → tests SMB credentials
```

---

##### SMB Version Using Metasploit
Start Metasploit:

```bash
msfconsole -q
```

Use:

```text
use auxiliary/scanner/smb/smb_version
```

Check options:

```text
show options
```

Set target:

```text
set RHOSTS demo.ine.local
```

Run:

```text
run
```

---

##### NetBIOS Name Enumeration
Using Nmap:

```bash
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
```

Using `nmblookup`:

```bash
nmblookup -A <target-ip>
```

If needed, resolve the hostname first:

```bash
getent hosts demo.ine.local
```

Then:

```bash
nmblookup -A <target-ip>
```

---

##### Anonymous SMB Access
Check whether shares can be listed without credentials:

```bash
smbclient -L demo.ine.local -N
```

Important options:

```text
-L → list available shares
-N → no password
```

If shares are returned without credentials:

```text
Anonymous SMB access → Allowed
```

Connect to a share:

```bash
smbclient //demo.ine.local/<share-name> -N
```

Useful commands:

```text
ls    → list files
pwd   → show current directory
cd    → change directory
get   → download file
put   → upload file if write access is allowed
exit  → close connection
```

Look for shares/files such as:

```text
backup
public
users
config
documents
*.txt
*.bak
*.conf
```

---

##### SMB Null Session Using rpcclient
Attempt a Null Session:

```bash
rpcclient -U "" -N demo.ine.local
```

Important options:

```text
-U "" → empty username
-N     → no password
```

Useful RPC commands:

```text
srvinfo       → server information
enumdomains   → enumerate domains
querydominfo  → domain information
enumdomusers  → enumerate domain users
netshareenum  → enumerate SMB shares
```

#### HTTP Enumeration
HTTP enumeration is used to gather information from a web server after discovering that an HTTP service is running.
##### Check Target Connectivity

Check if the target is reachable:

```bash
ping -c 5 victim-1
```

---

##### Start Metasploit

Open Metasploit:

```bash
msfconsole -q
```

---

##### Useful HTTP Auxiliary Modules

HTTP enumeration modules used 

```text
auxiliary/scanner/http/http_version
auxiliary/scanner/http/robots_txt
auxiliary/scanner/http/http_header
auxiliary/scanner/http/brute_dirs
auxiliary/scanner/http/dir_scanner
auxiliary/scanner/http/dir_listing
auxiliary/scanner/http/files_dir
auxiliary/scanner/http/http_put
auxiliary/scanner/http/http_login
auxiliary/scanner/http/apache_userdir_enum
```

What they do:

```text
http_version        → detects HTTP server/version
robots_txt          → retrieves and checks robots.txt
http_header         → retrieves HTTP headers
brute_dirs          → brute-forces common directories
dir_scanner         → searches for directories using a wordlist
dir_listing         → checks if directory listing is enabled
files_dir           → searches for files/directories
http_put            → checks PUT/DELETE methods and file upload capability
http_login          → tests HTTP authentication credentials
apache_userdir_enum → enumerates Apache user directories
```

---
##### HTTP Version

Use:

```text
use auxiliary/scanner/http/http_version
```

Set target:

```text
set RHOSTS victim-1
```

Run:

```text
run
```

This helps identify the web server and HTTP version information.

---

##### robots.txt Enumeration

Use:

```text
use auxiliary/scanner/http/robots_txt
```

Set target:

```text
set RHOSTS victim-1
```

Run:

```text
run
```

This checks the server's `robots.txt` file for paths or directories.

---

##### HTTP Header Enumeration

Use:

```text
use auxiliary/scanner/http/http_header
```

Set target:

```text
set RHOSTS victim-1
```

Run:

```text
run
```

To check headers of a specific path:

```text
set TARGETURI /secure
run
```

Important option:

```text
TARGETURI → specific web path to scan
```

---

##### Directory Brute Force

Use:

use auxiliary/scanner/http/brute_dirs

Set target:

```text
set RHOSTS victim-1
```

Run:

```text
run
```

This attempts to discover common hidden directories.

---

##### Directory Scanner

Use:

```text
use auxiliary/scanner/http/dir_scanner
```

Set target:

```text
set RHOSTS victim-1
```

Set dictionary:

```text
set DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
```

Run:

```text
run
```

Important option:

```text
DICTIONARY → wordlist used to search for directories
```

---

##### Directory Listing

Use:

```text
use auxiliary/scanner/http/dir_listing
```

Set target:

```text
set RHOSTS victim-1
```

Set path:

```text
set PATH /data
```

Run:

```text
run
```

This checks whether directory listing is enabled for the specified path.

---

##### File and Directory Enumeration

Use:

```text
use auxiliary/scanner/http/files_dir
```

Set target:

```text
set RHOSTS victim-1
```

Disable verbose output:

```text
set VERBOSE false
```

Run:

```text
run
```

---

##### HTTP PUT Method

Use:

```text
use auxiliary/scanner/http/http_put
```

Set target:

```text
set RHOSTS victim-1
```

Set upload path:

```text
set PATH /data
```

Set filename:

```text
set FILENAME test.txt
```

Set file content:

```text
set FILEDATA "Welcome To AttackDefense"
```

Run:

```text
run
```

If successful, the server allows writing files using HTTP PUT.

If the file already exists, it may be overwritten.

Verify the uploaded file:

```bash
wget http://victim-1:80/data/test.txt
cat test.txt
```

Expected content:

```text
Welcome To AttackDefense
```

---

##### HTTP DELETE Method

The same module can be used to delete the uploaded file.

Use:

```text
use auxiliary/scanner/http/http_put
```

Set options:

```text
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set ACTION DELETE
```

Run:

```text
run
```

Verify that the file was deleted:

```bash
wget http://victim-1:80/data/test.txt
```

If deletion was successful:

```text
404 Not Found
```

Important option:

```text
ACTION DELETE → deletes the specified file
```

---

##### HTTP Login Testing

Use:

```text
use auxiliary/scanner/http/http_login
```

Set target:

```text
set RHOSTS victim-1
```

Set authentication path:

```text
set AUTH_URI /secure/
```

Disable verbose output:

```text
set VERBOSE false
```

Run:

```text
run
```

Important option:

```text
AUTH_URI → path protected by HTTP authentication
```

---

##### Apache User Enumeration

Use:

```text
use auxiliary/scanner/http/apache_userdir_enum
```

Set username wordlist:

```text
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
```

Set target:

```text
set RHOSTS victim-1
```

Disable verbose output:

```text
set VERBOSE false
```

Run:

```text
run
```

This attempts to identify valid Apache user directories using the supplied username list.

#### MySQL Enumeration

MySQL enumeration is used to gather information from a MySQL service after discovering that it is running on the target.

##### Check Target Connectivity

Check if the target is reachable:

```bash
ping -c 4 demo.ine.local
```

---

##### Detect MySQL Service

Run an Nmap scan:

```bash
nmap demo.ine.local
```

This helps identify whether the MySQL service is running on the target.

---

##### Start Metasploit

Open Metasploit:

```bash
msfconsole -q
```

---

##### Useful MySQL Modules

MySQL modules used in this lab:

```text
auxiliary/scanner/mysql/mysql_version
auxiliary/scanner/mysql/mysql_login
auxiliary/admin/mysql/mysql_enum
auxiliary/admin/mysql/mysql_sql
auxiliary/scanner/mysql/mysql_file_enum
auxiliary/scanner/mysql/mysql_hashdump
auxiliary/scanner/mysql/mysql_schemadump
auxiliary/scanner/mysql/mysql_writable_dirs
```

What they do:

```text
mysql_version       → detects MySQL server version
mysql_login         → tests MySQL credentials
mysql_enum          → enumerates MySQL server information
mysql_sql           → executes SQL queries
mysql_file_enum     → checks for readable files
mysql_hashdump      → dumps MySQL password hashes
mysql_schemadump    → enumerates databases and schemas
mysql_writable_dirs → checks for writable directories
```

---

##### MySQL Version

Use:

```text
use auxiliary/scanner/mysql/mysql_version
```

Set target:

```text
set RHOSTS demo.ine.local
```

Run:

```text
run
```

This identifies the MySQL server version.

---

##### MySQL Login Testing

Use:

```text
use auxiliary/scanner/mysql/mysql_login
```

Set target:

```text
set RHOSTS demo.ine.local
```

Set username:

```text
set USERNAME root
```

Set password wordlist:

```text
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

Disable verbose output:

```text
set VERBOSE false
```

Run:

```text
run
```

Important options:

```text
USERNAME  → MySQL username to test
PASS_FILE → password wordlist
VERBOSE   → controls detailed output
```

---

##### MySQL Enumeration

After valid credentials are discovered, use:

```text
use auxiliary/admin/mysql/mysql_enum
```

Set credentials and target:

```text
set USERNAME root
set PASSWORD twinkle
set RHOSTS demo.ine.local
```

Run:

```text
run
```

This gathers information from the MySQL server using valid credentials.

---
##### Execute SQL Queries

Use:

```text
use auxiliary/admin/mysql/mysql_sql
```

Set credentials and target:

```text
set USERNAME root
set PASSWORD twinkle
set RHOSTS demo.ine.local
```

Run:

```text
run
```

This module can execute SQL queries against the MySQL server.

---

##### MySQL File Enumeration

Use:

```text
use auxiliary/scanner/mysql/mysql_file_enum
```

Set credentials and target:

```text
set USERNAME root
set PASSWORD twinkle
set RHOSTS demo.ine.local
```

Set file list:

```text
set FILE_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
```

Enable verbose output:

```text
set VERBOSE true
```

Run:

```text
run
```

Important option:

```text
FILE_LIST → list of files/paths to check
```

---

##### MySQL Hash Dump

Use:

```text
use auxiliary/scanner/mysql/mysql_hashdump
```

Set credentials and target:

```text
set USERNAME root
set PASSWORD twinkle
set RHOSTS demo.ine.local
```

Run:

```text
run
```

This attempts to dump MySQL user password hashes.

---

##### MySQL Schema Dump

Use:

```text
use auxiliary/scanner/mysql/mysql_schemadump
```

Set credentials and target:

```text
set USERNAME root
set PASSWORD twinkle
set RHOSTS demo.ine.local
```

Run:

```text
run
```

This enumerates MySQL database and schema information.

---

##### MySQL Writable Directories

Use:

```text
use auxiliary/scanner/mysql/mysql_writable_dirs
```

Set target and credentials:

```text
set RHOSTS demo.ine.local
set USERNAME root
set PASSWORD twinkle
```

Set directory list:

```text
set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
```

Run:

```text
run
```

Important option:

```text
DIR_LIST → list of directories to test for write access
```

#### SSH Enumeration

SSH enumeration is used to gather information from an SSH service after discovering that it is running on the target.

##### Check Target Connectivity

Check if the target is reachable:

```bash
ping -c 4 demo.ine.local
```

---

##### Detect SSH Service

Run an Nmap SYN scan with service detection:

```bash
nmap -sS -sV demo.ine.local
```

Important options:

```text
-sS → TCP SYN scan
-sV → service/version detection
```

This helps identify whether SSH is running and detects its version.

---

##### Start Metasploit

Open Metasploit:

```bash
msfconsole
```

---

##### Useful SSH Modules

SSH modules used in this lab:

```text
auxiliary/scanner/ssh/ssh_version
auxiliary/scanner/ssh/ssh_login
```

What they do:

```text
ssh_version → detects SSH server/version
ssh_login   → tests SSH credentials and can create a session after successful login
```

---

##### SSH Version

Use:

```text
use auxiliary/scanner/ssh/ssh_version
```

Set target:

```text
set RHOSTS demo.ine.local
```

Run:

```text
exploit
```

This identifies the SSH server version running on the target.

---

##### SSH Login Testing

Use:

```text
use auxiliary/scanner/ssh/ssh_login
```

Set target:

```text
set RHOSTS demo.ine.local
```

Set username wordlist:

```text
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
```

Set password wordlist:

```text
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt
```

Stop after finding valid credentials:

```text
set STOP_ON_SUCCESS true
```

Enable verbose output:

```text
set VERBOSE true
```

Run:

```text
exploit
```

Important options:

```text
USER_FILE       → username wordlist
PASS_FILE       → password wordlist
STOP_ON_SUCCESS → stops after finding valid credentials
VERBOSE         → controls detailed output
```

---

##### SSH Session

List available sessions:

```text
sessions
```

Interact with the SSH session:

```text
sessions -i 1
```

#### SMTP Enumeration

SMTP enumeration is used to gather information about an SMTP mail server, identify its software and hostname, enumerate valid users, and inspect supported SMTP commands.

##### Detect SMTP Service and Banner

Run Nmap service detection with the banner script:

```bash
nmap -sV --script banner demo.ine.local
```

Important options:

```text
-sV             → service/version detection
--script banner → retrieves service banner information
```

In this lab:

```text
Server → Postfix
Banner → openmailbox.xyz ESMTP Postfix: Welcome to our mail server.
```

---

##### Connect to SMTP Using Netcat

Connect directly to SMTP port `25`:

```bash
nc demo.ine.local 25
```

The SMTP banner can reveal the server hostname/domain.

In this lab:

```text
openmailbox.xyz
```

---

##### SMTP User Verification

SMTP `VRFY` can be used to check whether a user exists on the mail server.

Connect:

```bash
nc demo.ine.local 25
```

Check the `admin` user:

```text
VRFY admin@openmailbox.xyz
```

Result:

```text
admin → exists
```

Check another user:

```text
VRFY commander@openmailbox.xyz
```

Result:

```text
commander → does not exist
```

---

##### SMTP Capabilities

Connect using Telnet:

```bash
telnet demo.ine.local 25
```

Use:

```text
HELO attacker.xyz
EHLO attacker.xyz
```

What they do:

```text
HELO → introduces the client to the SMTP server
EHLO → introduces the client and requests extended SMTP capabilities
```

`EHLO` is useful for identifying supported SMTP commands and extensions.

---

##### SMTP User Enumeration Using smtp-user-enum

Use a username wordlist:

```bash
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local
```

Important options:

```text
-U → username wordlist
-t → target server
```

In this lab:

```text
Valid users → 8
```

---

##### SMTP User Enumeration Using Metasploit

Start Metasploit:

```bash
msfconsole -q
```

Use:

```text
use auxiliary/scanner/smtp/smtp_enum
```

Set target:

```text
set RHOSTS demo.ine.local
```

Run:

```text
exploit
```

The module uses a username list to identify valid SMTP users.

In this lab:

```text
Valid users → 22
```

---

##### Send Mail Using Telnet

Connect:

```bash
telnet demo.ine.local 25
```

Introduce the client:

```text
HELO attacker.xyz
```

Set sender:

```text
MAIL FROM: admin@attacker.xyz
```

Set recipient:

```text
RCPT TO: root@openmailbox.xyz
```

Start message data:

```text
DATA
```

Enter the message:

```text
Subject: Hi Root

Hello,
This is a fake mail sent using telnet command.

From,
Admin
.
```

Important:

```text
. → a single dot on the last line terminates the DATA section
```

---

##### Send Mail Using sendemail

Send the message directly from the command line:

```bash
sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no
```

Important options:

```text
-f → sender email address
-t → recipient email address
-s → SMTP server
-u → email subject
-m → email message
-o tls=no → disables TLS
```

