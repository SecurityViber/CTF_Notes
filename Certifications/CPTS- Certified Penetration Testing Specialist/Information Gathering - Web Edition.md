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

![[Pasted image 20250127185059.png]]

## Passive reconnaissance

![[Pasted image 20250127185152.png]]


## Whois

![[Pasted image 20250127192016.png]]



## DNS 

How a DNS zone file looks like 
![[Pasted image 20250127193822.png]]


Record Types 

![[Pasted image 20250127193933.png]]


DNS Tools 

![[Pasted image 20250128194804.png]]


Dig

![[Pasted image 20250128195312.png]]


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


![[Pasted image 20250128200340.png]]


You can request a zone transfer by `dig axfr @nsztm1.digi.ninja zonetransfer.me


## Virtual Hosts

![[Pasted image 20250128212345.png]]


![[Pasted image 20250128212723.png]]


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


![[Pasted image 20250129213510.png]]

Commonly used Tools for Fingerprinting

![[Pasted image 20250129213528.png]]


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

![[Pasted image 20250130122543.png]]


.well-known
![[Pasted image 20250130122932.png]]

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

![[Pasted image 20250130123820.png]]



## Automating Recon

Doing all this manual Reconnaissance is super annoying... what about just using some tools to do it?

- FinalRecon -> This one looks siiicko!!!
- Recon-ng
- theHarvester
- SpiderFoot
- OSINT Framework


![[Pasted image 20250130125724.png]]


## Questions 

- Which Bullet Proof hoster do exist, that I know?
- What is a Web Application Firewall actually doing? And how does it differ from a regular firewall?
- How is this OpenID Connect Stuff working? And how can I make the keycloak stuff finally running?
- How does this Maltego Stuff actually work?