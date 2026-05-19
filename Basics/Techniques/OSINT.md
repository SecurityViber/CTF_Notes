---
tags: [basics, technique, osint, recon]
---


> [!info] See also: [[Information Gathering - Web Edition]]



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

## Subdomain Enumeration

```bash
# Certificate Transparency (passive)
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sort -u

# WHOIS
whois target.com

# DNS brute force
dnsenum --dnsserver <ns> --enum -f ~/wordlists/subdomains.txt target.com
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u https://target.com -H 'Host: FUZZ.target.com' -mc 200
```

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

## Metadata Extraction

```bash
# Extract metadata from documents
exiftool document.pdf

# Strip metadata before sharing
exiftool -all= document.pdf
```