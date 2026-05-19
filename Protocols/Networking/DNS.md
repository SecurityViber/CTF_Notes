---
tags: [basics, protocol, dns]
---


## DNS Overview 

DNS Zone file structure:
![[dns_zone_file.png]]

Record types:
![[dns_record_types.png]]

DNS Tools:
![[dns_tools.png]]

dig options:
![[dig_tool_options.png]]


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


## DNS Zone Transfer 

![[dns_zone_transfer.png]]

A misconfigured DNS server may serve `AXFR` to anyone, leaking the full zone file:

```bash
dig axfr @nsztm1.digi.ninja zonetransfer.me
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


## Subdomain enumeration 

Tools you could use for DNS enumeration:
- dnsenum
- fierce
- dnsrecon
- amass
- assetfinder
- puredns


```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```


## Basic Investigation Commands


```sh
# Check the TXT entries for a record
dig TXT _acme-challenge.example.com +short

# Resolve the TXT entrie for a record over a specific dns server 
dig TXT _acme-challenge.example.com @8.8.8.8

# TXT records using nslookup using the dns server 8.8.8.8 
nslookup -type=TXT _acme-challenge.example.com 8.8.8.8
```


## Defensive Reading

- [Tips to Protect the DNS from Data Exfiltration (DarkReading)](https://www.darkreading.com/cyber-risk/tips-to-protect-the-dns-from-data-exfiltration)
