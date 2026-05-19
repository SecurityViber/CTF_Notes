---
tags: [basics, protocol, dns]
---


> [!info] See also: [[Basics/Techniques/DNS Records|DNS Records (technique)]]



## DNS 

```bash 
# bind9 config files 
/etc/bind/named.conf.local
/etc/bind/named.conf.options
/etc/bind/named.conf.log

# Footprinting the service 
dig ns inlanefreight.htb @<ip> 
dig CH TXT version.bind <ip>
dig any inlanefreight.htb @<ip>

# Footprinting
# NS request to the specific nameserver
dig ns <domain.tld> @<nameserver> 

# ANY request to the specific nameserver 
dig any <domain.tld> @<nameserver>

# AXFR request to the specific nameserver 
dig axfr <domain.tld> @<nameserver>

# Subdomain brute forcing 
dnsenum --dnsserver <nameserver> --enum -p 0 -s 0 -o found_subdomains.txt -f ~/subdomains.list <domain.tld>
```


## Common Tools 
### Essentials
- dig
- host
- nslookup
- whois

### Enumeration and Recon 
- amass
- subfinder
- dnsrecon
- dnsenum
- massdns


## Basic investigation commands


```sh
# Check the TXT entries for a record
dig TXT _acme-challenge.example.com +short

# Resolve the TXT entrie for a record over a specific dns server 
dig TXT _acme-challenge.example.com @8.8.8.8

# TXT records using nslookup using the dns server 8.8.8.8 
nslookup -type=TXT _acme-challenge.example.com 8.8.8.8
