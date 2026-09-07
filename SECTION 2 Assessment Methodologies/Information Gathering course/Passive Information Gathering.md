#### Website Recon & Footprinting

##### Host Command
Resolves a domain name to its corresponding IP address via DNS.  
  
```bash  
host example.com
host -t mx example.com
host -t ns example.com
host -t txt example.com
```

##### robots.txt
A file used to instruct search engine crawlers which pages or directories should not be accessed or indexed, and can be used in penetration testing to identify hidden or sensitive paths.

Access: target.com/robots.txt

##### sitemap
A file that lists website pages to help search engines index content, and can be used in penetration testing to discover hidden pages and paths.

Access:
target.com/sitemap.xml  
target.com/sitemap_index.xml  
target.com/sitemap-index.xml

##### BuiltWith 
A tool used to identify technologies used by a website (e.g., CMS, frameworks, libraries), and can be used in penetration testing to understand the target stack.  
  
Access:  
https://builtwith.com and install extension or open the site

##### Wappalyzer
A tool used to identify technologies used by a website (e.g., CMS, frameworks, libraries) via browser extension, and can be used in penetration testing to analyze the target stack.  

Access:  
Install Wappalyzer extension (Chrome / Firefox)

##### HTTrack
A website mirroring tool used to clone target websites locally, enabling offline analysis of structure, endpoints, and hidden resources. Commonly used in penetration testing to map attack surface and discover unlinked or sensitive files.

Access:
Install HTTrack (GUI) or install via terminal:
sudo apt install httrack  
then run:
httrack target.com

##### WhatWeb
A reconnaissance tool used to fingerprint target websites by identifying underlying technologies such as CMS, web servers, frameworks, plugins, and sometimes version details. Helps penetration testers understand the target environment and identify potential attack vectors.

```bash
whatweb target.com
```

#### Whois Enumeration

##### Whois
A simple reconnaissance tool used to get basic domain information such as the registrar, owner (if not hidden), creation and expiration dates, and name servers.

In penetration testing, it helps identify who owns the target, check how old the domain is, and discover related infrastructure.

Access (Web):
https://whois.domaintools.com

```bash
whois target.com
```

#### Website Footprinting With Netcraft

##### Netcraft
A passive reconnaissance tool used to gather information about websites such as hosting provider, web server, technologies, and security details without directly interacting with the target.

In penetration testing, it helps identify the target’s infrastructure and technologies safely without leaving traces.

Access (Web):
https://sitereport.netcraft.com

#### DNS Recon (passive)
##### Dnsrecon tool
A DNS enumeration tool in Kali Linux used to gather DNS records such as A, MX, NS, and TXT, and to discover subdomains.  
  
In penetration testing, it helps map the target’s DNS and find hidden assets like subdomains and mail servers.  
  
```bash  
dnsrecon -d target.com
```

Cloudflare Note:
Cloudflare hides the real IP of websites by proxying A records (web traffic) through its network.

However, MX records (mail servers) are not proxied because they require direct communication, so their real IP addresses remain exposed in DNS.

This allows Dnsrecon to retrieve MX records and potentially reveal real infrastructure IPs even if the website is behind Cloudflare.
##### DNSDumpster
DNSDumpster is a passive recon tool used early to understand a target domain without directly interacting with it  
You input a domain and it returns subdomains DNS records like A MX NS TXT plus related IPs and basic infrastructure mapping  

In penetration testing this gives you external visibility it helps you identify exposed services understand structure and spot potential misconfigurations  

Usage:
Go to https://dnsdumpster.com  
Enter the target domain  
Review hosts records and the network map  

Key Points:
Passive no direct traffic to target  
Builds a visual map of infrastructure  
Finds subdomains and exposed IPs  

Practical Insight:
Even behind Cloudflare you may still see mail servers unproxied subdomains or old misconfigured records  

Limitation:
Not 100 percent accurate  
Misses private or hidden infrastructure  
Depends on public data sources only 
#### WAF With wafw00f

##### wafw00f
A reconnaissance tool in Kali Linux used to detect and identify Web Application Firewalls (WAFs) protecting websites.  
  
In penetration testing, it helps identify defensive layers like Cloudflare, AWS WAF, Akamai, or ModSecurity before active scanning or exploitation.  

GitHub:
https://github.com/EnableSecurity/wafw00f

```bash
wafw00f target.com
```

Verbose mode:
```bash
wafw00f -v target.com
```

Find all possible WAF detections:
```bash
wafw00f -a target.com
```

Multiple targets:
```bash
wafw00f -i targets.txt
```

Practical Insight:
Detecting a WAF is important because some security devices may:
block payloads
rate limit requests
return fake responses
ban your IP during scanning

Limitation:
Fingerprint-based detection only  
May miss custom WAFs  
Does not bypass the WAF itself

#### Subdomain Enumeration With Sublist3r
##### Sublist3r
A subdomain enumeration tool in Kali Linux used to discover subdomains of target domains using passive OSINT sources like search engines and public databases.  
  
In penetration testing, it helps identify hidden assets such as admin panels, staging servers, APIs, and development environments.  

GitHub:
https://github.com/aboul3la/Sublist3r

```bash
sublist3r -d target.com
```

Save output to file:
```bash
sublist3r -d target.com -o results.txt
```

Enable brute force:
```bash
sublist3r -d target.com -b
```

Use multiple threads:
```bash
sublist3r -d target.com -t 50
```

Practical Insight:
Subdomains may expose :
admin panels
old systems
development environments
forgotten services
APIs

Cloudflare Note:
Even if the main website is behind Cloudflare some subdomains may :
bypass Cloudflare
expose real IPs
point to old infrastructure
use different security settings

Limitation:
Depends heavily on public sources  
May miss private or hidden subdomains  
Brute force mode is slower and more detectable

#### Google Dorks
##### Google Dorking
A reconnaissance technique that uses advanced Google search operators to discover sensitive information indexed by search engines.  
  
In penetration testing, it helps find exposed files, login pages, backups, admin panels, documents, and misconfigured directories without directly attacking the target.

Common Operators:

site:
Limits search results to a specific domain.

Used in penetration testing to enumerate indexed pages and subdomains of a target.

```text
site:target.com
site:target.com admin
```

---
inurl:
Searches for specific words inside URLs.

Used to find:
login pages
admin panels
APIs
vulnerable parameters

```text
inurl:admin
site:target.com inurl:login
```

Example:
```text
inurl:auth_user_file.txt
```

This may help discover sensitive authentication files exposed online.

---

intitle:
Searches for keywords inside page titles.

Used to identify:
admin dashboards
default pages
exposed panels
devices

```text
intitle:"index of"
intitle:"login page"
```

---

index of
Searches for open directory listings enabled on web servers.

Used to discover:
backups
uploads
configuration files
logs
sensitive documents

```text
intitle:"index of"
intitle:"index of" backup
```

---

filetype:
Searches for specific file extensions.

Used to find exposed:
PDFs
SQL files
configuration files
backups
documents

```text
filetype:pdf site:target.com
filetype:sql
filetype:env
```

---

Google Hacking Database (GHDB)
A public database containing known Google Dorks used to discover sensitive information and common misconfigurations indexed by Google.

Used by penetration testers to:
speed up reconnaissance
discover exposed data
find common vulnerabilities

Access:
https://www.exploit-db.com/google-hacking-database

Practical Insight:
Many leaks happen because:
files are publicly indexed
backups are exposed
directory listing is enabled
sensitive documents are searchable by Google

Limitation:
Results depend on Google indexing  
Some data may be outdated or removed  
Many modern websites block indexing of sensitive pages

---
##### Wayback Machine
A web archive service that stores historical snapshots of websites over time.

Used in penetration testing to discover:
old pages
removed content
previous login portals
exposed files
old APIs
forgotten endpoints

Access:
https://web.archive.org

Example Use:
Search target.com and review old snapshots.

Practical Insight:
Sometimes sensitive files or admin panels removed from the live website may still appear in archived snapshots.

#### Email Harvesting With theHarvester
##### theHarvester
An OSINT and passive reconnaissance tool in Kali Linux used to gather public information about a target such as:
- email addresses
- subdomains
- hostnames
- employee information
- public infrastructure

In penetration testing, it helps build an external profile of the target without directly interacting with systems aggressively.

GitHub:
https://github.com/laramies/theHarvester

---

Basic search:
```bash
theHarvester -d target.com -b google
```

Explanation:
`-d` specifies the target domain
`-b` specifies the data source

This searches Google for publicly indexed information related to the target.

This is passive information gathering because the tool collects publicly available data without directly interacting with the target infrastructure.

Used in real pentests to:
- discover emails
- find subdomains
- understand external exposure

---

Use different OSINT sources:
```bash
theHarvester -d target.com -b bing
theHarvester -d target.com -b yahoo
theHarvester -d target.com -b crtsh
```

Explanation:
Different sources contain different data.

Example:
- `google` → indexed pages
- `crtsh` → SSL certificate transparency logs
- `bing` / `yahoo` → additional indexed results

This remains passive information gathering because data is collected from public external sources.

Used in real engagements because:
one source may miss assets another source can reveal.

---

Limit number of results:
```bash
theHarvester -d target.com -b google -l 100
```

Explanation:
- `-l` limits the number of collected results

Used to:
- speed up reconnaissance
- reduce noise
- avoid huge result sets

---

Save results:
```bash
theHarvester -d target.com -b google -f results
```

Explanation:
- `-f` saves results into output files

Used in real pentests for:
- reporting
- documentation
- later analysis
- importing into other tools

---

Enable DNS lookup:
```bash
theHarvester -d target.com -b google -n
```

Explanation:
- `-n` performs DNS lookup on discovered hosts

This changes the tool from passive information gathering into semi-active reconnaissance because it starts sending direct DNS queries to resolve discovered hosts.

This helps resolve:
```text
subdomain → IP address
```

Used in practice to:
- identify public infrastructure
- detect hosting providers
- find exposed servers

---

DNS brute force:
```bash
theHarvester -d target.com -b google -c
```

Explanation:
- `-c` enables DNS brute force

This changes the tool into active information gathering because it actively guesses and tests subdomains through direct DNS requests.

The tool starts guessing common subdomains such as:
```text
admin
vpn
mail
dev
api
```

Then checks if they exist through DNS queries.

Used in real pentests to:
- discover hidden assets
- find forgotten systems
- locate internal-looking services exposed externally

---

Use Shodan integration:
```bash
theHarvester -d target.com -b google -s
```

Explanation:
- `-s` queries Shodan for discovered hosts

This remains mostly passive information gathering because the tool searches Shodan databases instead of directly scanning the target.

This may reveal:
- open ports
- services
- banners
- exposed technologies

Used in real assessments to:
- understand exposed infrastructure
- identify risky internet-facing services

---

API endpoint scan:
```bash
theHarvester -d target.com -a
```

Explanation:
- `-a` scans for possible API endpoints

This is more active than normal passive information gathering because the tool may directly probe discovered domains searching for API-related paths and endpoints.

Used to discover:
- public APIs
- forgotten endpoints
- developer interfaces

Important because APIs often expose:
- sensitive data
- authentication flaws
- misconfigurations

---

Practical Insight:
theHarvester is commonly used during the early reconnaissance phase because it is:
- passive
- low noise
- fast
- useful for mapping attack surface

However some options change the behavior:
- `-n` → semi-active reconnaissance
- `-c` → active reconnaissance
- `-a` → more active probing behavior

---

Real World Usage:
In real web pentests and red team engagements it is commonly used to:
- collect employee emails
- identify naming conventions
- map subdomains
- discover exposed infrastructure
- prepare phishing simulations
- identify attack surface before active testing

---

Limitation:
Depends heavily on public OSINT sources  
Some features require API keys  
Results may be incomplete or outdated


#### Leaked Password Databases

##### Have I Been Pwned (HIBP)
A public OSINT service used to check whether emails or passwords appeared in known leaked databases and data breaches.  
  
In penetration testing, it helps identify exposed employee accounts and password reuse risks.

Access:
https://haveibeenpwned.com

Example:
```text
employee@target.com
```

Passive Recon Note:
This is passive reconnaissance because information is collected from public leaked databases without directly interacting with the target.

Practical Insight:
Leaked credentials may help identify:
- password reuse
- phishing risks
- exposed employee accounts

Other Common Leak Sources:
- DeHashed
- LeakCheck
- Intelligence X