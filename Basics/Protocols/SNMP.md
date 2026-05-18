---
tags: [basics, protocol, snmp]
---

[[Basics/Index|← Basics]]



- Concepts -> 
	- SNMP Traps
	- SNMP Polling

- Communication on Port 161




## Commonly used commands 

```bash
# Using tool snmpwalk
snmpwalk -v 2c -c public ip 1.3.6.1.2.1.1.5.0

# Using tool onesixtyone
onesyxtyone -c dict.txt <ip>

snmpwalk -v2c -c <community string> <FQDN/IP> 
onesixtyone -c <community-strings.loist <FQDN/IP>
braa <community string>@<FQDN/IP>:.1.*


```