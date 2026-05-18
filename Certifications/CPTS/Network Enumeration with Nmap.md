---
tags:
  - questions
  - nmap
  - netcat
  - evasion
  - ids-ips
---

[[Certifications/CPTS/Index|← CPTS]]

**--> Currently at lesson "Host and Port Scanning"**


Nmap Scan Types
- Host discovery
- Port scanning
- Service enumeration and detection
- OS detection
- Scriptable interaction with the target service (Nmap Scripting Engine)


`nmap <scan types> <options> <target>`


- TCP-SYN scan (-sS)
	- Receiving SYN-ACK -> Port is open
	- Receiving RST -> Port is closed 
	- No packet at all -> Filtered (by firewall or anything else)




```sh
# Scanning network range, with disabled port scan and output it to tnet file (This is Host discovery with ICMP)
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

# Take a file with a list of hosts -> host.lst
sudo nmap 10.129.2.0/24 -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

# disable portscanning (-sn), store result in hosts (-oA), perform ping scan using ICMP Echo requests (-PE), Show all packets sent and received (--packet-trace)
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace

#
sudo nmap 10.129.2.18 -sn -oA host -PE --reason

# disable the arp ping + trace the packets
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping

# Clear view of Syn scan by disabling the ICMP echo request (-Pn), DNS resolution (-n) and the ARP ping scan (--disable-arp-sping)
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping 

```



## Host and Port Scanning 


Possible Port states 
![[nmap_port_states.png]]



## Outputs 

- -oN -> Normal nmap output
- -oG -> Grepable output -> .gnmap
- -oX -> XML output 
- -oA -> Create all the three output files 


- XML can easily be converted to html by `xsltproc target.xml -o target.html`


- Using the `-v` verbose flag logs you directly the ports when they are found! Increase the verbosity by using `vv`



## Nmap Scripting Engine 

Or also called NSE 

![[nmap_scripting_engine.png]]





```bash

sudo nmap <target> -sC 
sudo nmap <target> --script <category> 

# Executing simple scripts 
sudo nmap <target> --script <script-name>,<script-name>
sudo nmap <target> -p 25 --script banner,smtp-commands

## Searching for Vulnerabilities 
sudo nmap <target> -p 80 -sV --script vuln

```



## Performance 

Stuff you can tweak
- Speed -> -T <0-5> 
- Frequency -> --min-parallelism number
- Timeouts -> --max-rtt-timeout time
- Scan the top 100 ports -> -F


## Firewall and IDS/IPS Evasion 

For Firewalls it's hard to block ACK scans (-sA), since usually the ACK packages are routed through. But the default SYN (-sS) or Connect (-sT) scan will easily be detected and dropped imediately.

- You can also create Decoy Scans -D RND:5 
	- With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses.


DNS Proxying
By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target.


Sicko command to avoid detetion

`sudo nmap -g53 --max-retries=1 -Pn -p- --disable-arp-ping 10.129.2.47`


How to connect with netcat using a specific interface, going  via port 53, but connecting to port 50000

````shell
sudo nc -nv -s PWNIP -p53 STMIP 50000
````




## Links 

- [Host Discovery Strategies](https://nmap.org/book/host-discovery-strategies.html)
- [Table for TTL to OS Types](https://ostechnix.com/identify-operating-system-ttl-ping/)
- [Port Scanning Techniques](https://www.hackercoolmagazine.com/port-scanning-techniques-for-beginners/)


## Questions 

- What is the Nmap Scripting engine?
- What is an UDP Datagram?
- What is an SCTP association
- How to avoid Firewalls during the scan... still not 100% clear
	- https://nmap.org/book/man-bypass-firewalls-ids.html
- 

