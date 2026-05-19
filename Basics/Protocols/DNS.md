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