---
tags: [basics, technique, web, recon, fingerprinting]
---


> [!info] See also: [[EyeWitness]] · [[Directory Busting]]


## Why Fingerprint

![[web_fingerprinting_benefits.png]]


## Techniques 

![[web_fingerprinting_techniques.png]]


## Tools 

![[fingerprinting_tools.png]]


### Whatweb 

```bash
whatweb --no-errors <ip address, or range>
```


### EyeWitness 
Can be used to take Screenshots of Websites and get required information.
https://github.com/RedSiege/EyeWitness

```bash
./EyeWitness -f urls.txt --web

./EyeWitness -x urls.xml --timeout 8 

./EyeWitness.py -f urls.txt --web --proxy-ip 127.0.0.1 --proxy-port 8080 --proxy-type socks5 --timeout 120
```


### Banner Grabbing & WAF Detection

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

How you can configure a robots.txt file:

![[robots_txt_configuration.png]]


.well-known paths:

![[well_known_paths.png]]


Common crawling tools:
- Burp Suite Spider
- OWASP ZAP (Zed Attack Proxy)
- Scrapy (Python framework)
- Apache Nutch (scalable crawler)


```bash 
pip3 install scrapy

wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip

python3 ReconSpider.py http://inlanefreight.com
```
