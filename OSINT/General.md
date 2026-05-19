---
tags: [basics, technique, osint, recon]
---


## Active vs. Passive Reconnaissance 

![[active_reconnaissance.png]]

![[passive_reconnaissance.png]]


## WHOIS

![[whois_lookup.png]]

```bash
whois target.com
```


## Certificate / Domain Intelligence

| Tool | Use |
| ---- | --- |
| [crt.sh](https://crt.sh) | Search Certificate Transparency logs → find subdomains |
| [Shodan](https://shodan.io) | Search for internet-exposed services and banners |
| `dig` / `nslookup` | DNS record lookup |
| [DNSDumpster](https://dnsdumpster.com) | DNS recon and subdomain discovery |
| [VirusTotal](https://virustotal.com) | Check domain/IP reputation, find subdomains |
| [SecurityTrails](https://securitytrails.com) | Historical DNS, WHOIS data |
| [Censys](https://censys.io) | Like Shodan, scans TLS/SSL certs and banners |


### Certificate Transparency Logs (crt.sh)

```bash
# All hostnames seen for a domain
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sort -u

# Filter for a substring (e.g. "dev")
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[] | select(.name_value | contains("dev")) | .name_value' | sort -u
```


## Subdomain Enumeration

```bash
# DNS brute force
dnsenum --dnsserver <ns> --enum -f ~/wordlists/subdomains.txt target.com
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u https://target.com -H 'Host: FUZZ.target.com' -mc 200
```

See [[DNS]] for more recon commands (zone transfer, dnsenum, dig, etc.).


## Social Media / People OSINT

| Platform | What to look for |
| -------- | ---------------- |
| LinkedIn | Employee names, job titles, tech stack clues, org structure |
| GitHub | Leaked credentials, internal repos, commit history |
| Twitter/X | Operational security mistakes, announcements |
| Facebook | Personal info, relationships |
| Instagram | Location data, personal life |


## Email & Username Intelligence

```bash
# Find email pattern from known employees
# Then enumerate with tools like:
theHarvester -d target.com -b google,linkedin,bing

# Username enumeration across platforms
# Use sherlock:
python3 sherlock username
```


## Search Engine Discovery 

![[google_search_operators.png]]


## Automating Recon

Doing all this manual reconnaissance is super annoying... what about just using some tools to do it?

- FinalRecon
- Recon-ng
- theHarvester
- SpiderFoot
- OSINT Framework

![[osint_framework.png]]


## Metadata Extraction

```bash
# Extract metadata from documents
exiftool document.pdf

# Strip metadata before sharing
exiftool -all= document.pdf
```


## Historical Incident References

- [Equifax Hack Post-Mortem (ZDNet)](https://www.zdnet.com/article/us-government-releases-post-mortem-report-on-equifax-hack/)
