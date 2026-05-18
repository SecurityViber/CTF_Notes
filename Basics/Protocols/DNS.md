---
tags: [basics, protocol, dns]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Basics/Techniques/DNS Records|DNS Records (technique)]]



## DNS 

```bash 
# bind9 config files 
/etc/bind/named.conf.local
/etc/bind/named.conf.options
/etc/bind/named.conf.log

# bind9 local configuration
/etc/bind/named.conf.local

# Footprinting the service 
dig ns inlanefreight.htb @<ip> 
dig CH TXT versionbind <ip>
dig any inlanefreight.htb @<ip>

# Footprinting
# NS request to the specifici nameserver
dig ns <domain.tld> @<nameserver> 

# ANY request to the specific nameserver 
dig any <domain.tld> @<nameserverv>

# AXFR request to the specific nameserver 
dig axfr <domain.tld> @<nameserver>

# Subdomain brute forcing 
dnsenum --dnsserver <nameserver> --enum -p 0 -s 0 -o found_subdomains.txt -f ~/subdomains.list <domain.tld>
```