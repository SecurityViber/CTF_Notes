---
tags: [basics, protocol, snmp]
---




- Concepts -> 
	- SNMP Traps
	- SNMP Polling

- Communication on Port 161




## Commonly used commands 

```bash
# Using tool snmpwalk
snmpwalk -v 2c -c public ip 1.3.6.1.2.1.1.5.0

# Using tool onesixtyone
onesixtyone -c dict.txt <ip>

snmpwalk -v2c -c <community string> <FQDN/IP> 
onesixtyone -c community-strings.list <FQDN/IP>
braa <community string>@<FQDN/IP>:.1.*


```