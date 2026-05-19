---
tags: [basics, protocol, snmp]
---


> [!info] See also: [[snmpwalk]] · [[onesixtyone]] · [[braa]]


- Concepts -> 
	- SNMP Traps
	- SNMP Polling

- Communication on Port 161 (UDP)




## Commonly used commands 

```bash
# Using snmpwalk to read OIDs
snmpwalk -v 2c -c public <ip> 1.3.6.1.2.1.1.5.0

# Using onesixtyone to brute force community strings
onesixtyone -c dict.txt <ip>

# Generic enum
snmpwalk -v2c -c <community string> <FQDN/IP> 
onesixtyone -c community-strings.list <FQDN/IP>

# Fast bulk OID retrieval with braa
braa <community string>@<FQDN/IP>:.1.*
```


## Tools to interact with the service 

- [[snmpwalk]] — walk OIDs (single host, focused)
- [[onesixtyone]] — fast community-string brute force
- [[braa]] — high-speed bulk OID scanner
