#### NetExec Commands

##### General Commands

```
nxc --help
```

```
nxc --version
```

```
nxc <protocol> --help
```

Examples:

```
nxc smb --help
nxc ldap --help
nxc winrm --help
nxc ssh --help
```

---

##### Target Selection

Single target:

```
nxc smb <target>
```

Network range:

```
nxc smb <network>/<cidr>
```

Example:

```
nxc smb 192.168.1.0/24
```

Targets from a file:

```
nxc smb targets.txt
```

---

##### SMB Enumeration

Enumerate SMB information:

```
nxc smb <target>
```

Check anonymous authentication:

```
nxc smb <target> -u '' -p ''
```

Enumerate anonymous shares:

```
nxc smb <target> -u '' -p '' --shares
```

Enumerate shares with credentials:

```
nxc smb <target> -u <user> -p '<password>' --shares
```

Show readable shares:

```
nxc smb <target> -u <user> -p '<password>' --shares READ
```

Show writable shares:

```
nxc smb <target> -u <user> -p '<password>' --shares WRITE
```

Enumerate the password policy:

```
nxc smb <target> -u <user> -p '<password>' --pass-pol
```

Enumerate local groups:

```
nxc smb <target> -u <user> -p '<password>' --local-group
```

---

##### Domain Authentication

```
nxc smb <target> -d <domain> -u <user> -p '<password>'
```

Example structure:

```
nxc smb <target> -d <domain.local> -u <username> -p '<password>'
```

---

##### Local Authentication

```
nxc smb <target> -u <local-user> -p '<password>' --local-auth
```

```
-d <domain>  → Domain authentication
--local-auth → Local authentication
```

---

##### LDAP Enumeration

Enumerate domain users:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --users
```

Enumerate active users:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --active-users
```

Export users:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --users-export users.txt
```

Enumerate domain groups:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --groups
```

Enumerate members of a specific group:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --groups "<group-name>"
```

Example:

```
nxc ldap <dc-ip> -u <user> -p '<password>' --groups "Domain Admins"
```

---

##### WinRM Authentication

```
nxc winrm <target> -u <user> -p '<password>'
```

Domain authentication:

```
nxc winrm <target> -d <domain> -u <user> -p '<password>'
```

---

##### SSH Authentication

```
nxc ssh <target> -u <user> -p '<password>'
```

---

##### FTP Authentication

```
nxc ftp <target> -u <user> -p '<password>'
```

Anonymous FTP check:

```
nxc ftp <target> -u anonymous -p anonymous
```

---

##### MSSQL Authentication

```
nxc mssql <target> -u <user> -p '<password>'
```

Domain authentication:

```
nxc mssql <target> -d <domain> -u <user> -p '<password>'
```

---

##### NetExec Modules

List modules:

```
nxc smb -L
```

Show module options:

```
nxc smb -M <module-name> --options
```

Run a module:

```
nxc smb <target> -u <user> -p '<password>' -M <module-name>
```

Run a module with options:

```
nxc smb <target> -u <user> -p '<password>' -M <module-name> -o KEY=value
```

---

##### Logging

Save output:

```
nxc smb <target> --log netexec-output.log
```

Verbose output:

```
nxc smb <target> --verbose
```

Debug output:

```
nxc smb <target> --debug
```

---

##### Database Commands

Open the database:

```
nxcdb
```

Inside `nxcdb`:

```
help
workspace list
workspace create <name>
workspace <name>
proto smb
hosts
shares
back
exit
```

---

##### Quick Command List

```
# SMB discovery
nxc smb <target>

# Anonymous SMB
nxc smb <target> -u '' -p ''

# Anonymous shares
nxc smb <target> -u '' -p '' --shares

# Credential check
nxc smb <target> -d <domain> -u <user> -p '<password>'

# Local credential check
nxc smb <target> -u <user> -p '<password>' --local-auth

# Share enumeration
nxc smb <target> -u <user> -p '<password>' --shares

# Password policy
nxc smb <target> -u <user> -p '<password>' --pass-pol

# Local groups
nxc smb <target> -u <user> -p '<password>' --local-group

# Domain users
nxc ldap <dc-ip> -u <user> -p '<password>' --users

# Active users
nxc ldap <dc-ip> -u <user> -p '<password>' --active-users

# Domain groups
nxc ldap <dc-ip> -u <user> -p '<password>' --groups

# WinRM check
nxc winrm <target> -u <user> -p '<password>'

# SSH check
nxc ssh <target> -u <user> -p '<password>'

# List SMB modules
nxc smb -L

# Open database
nxcdb
```