---
tags:
  - recon
  - whois
  - bullet_proof
  - questions
  - DNS
  - virtual_hosting
  - fingerprinting
  - banner_grabbing
  - web_crawling
  - search_operator
  - tools
---






## Active reconnaissance 

![[active_reconnaissance.png]]

## Passive reconnaissance

![[passive_reconnaissance.png]]


## Whois

![[whois_lookup.png]]



## DNS 

How a DNS zone file looks like 
![[dns_zone_file.png]]


Record Types 

![[dns_record_types.png]]


DNS Tools 

![[dns_tools.png]]


Dig

![[dig_tool_options.png]]


## Subdomain enumeration 


Tools you could use for dns enumeration:
- dnsenum
- fierce
- dnsrecon
- amass
- assetfinder
- puredns


```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```


## DNS Zone Transfer 


![[dns_zone_transfer.png]]


You can request a zone transfer by `dig axfr @nsztm1.digi.ninja zonetransfer.me


## Virtual Hosts

![[virtual_hosts_overview.png]]


![[virtual_hosts_diagram.png]]


Discovery done by:
- gobuster
- Feroxbuster
- ffuf

```bash
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

## Certificate Transparency Logs



```bash
curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
```


## Fingerprinting 

What are some commone Fingerprinting Techniques?


![[web_fingerprinting_techniques.png]]

Commonly used Tools for Fingerprinting

![[fingerprinting_tools.png]]


Some Commands you can use 

```bash 

# Banner Grabbing 
curl -I inlanefreight.com

# Detect presence of a WAF 
pip3 install git+https://github.com/EnableSecurity/wafw00f
wafw00f inlanefreight.com


# Using the webscanner Nikto 
git clone https://github.com/sullo/nikto
nikto -h inlanefreight.com -Tuning b


```


## Crawling

How you an configure a robots.txt file 

![[robots_txt_configuration.png]]


.well-known
![[well_known_paths.png]]

Common Crawling tools are 

- Burp Suite Spider
- OWASP ZAP (Zed Attack Proxy)
- Scrapy (Python Framework)
- Apache Nutch (Scalable Crawler)



``` bash
pip3 install scrapy

wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip

python3 ReconSpider.py http://inlanefreight.com

```



## Search Engine Discovery 

Search Operators

![[google_search_operators.png]]



## Automating Recon

Doing all this manual Reconnaissance is super annoying... what about just using some tools to do it?

- FinalRecon -> This one looks siiicko!!!
- Recon-ng
- theHarvester
- SpiderFoot
- OSINT Framework


![[osint_framework.png]]


## Questions 

- Which Bullet Proof hoster do exist, that I know?
- What is a Web Application Firewall actually doing? And how does it differ from a regular firewall?
- How is this OpenID Connect Stuff working? And how can I make the keycloak stuff finally running?
- How does this Maltego Stuff actually work?