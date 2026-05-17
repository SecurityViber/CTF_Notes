

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

```