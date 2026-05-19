---
tags: [basics, tool, recon, scanning]
---


> [!info] See also: [[Network Analysis]]

 
## Scan Types 

1. Host discovery
2. Port scanning
3. Service enumeration and detection
4. OS detection
5. Scriptable interaction (Nmap Scripting Engine)

Syntax: `nmap <scan types> <options> <target>`


## Possible Port States 

![[nmap_port_states.png]]

| State              | Description                                                                                                                                                                                          |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| open               | This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams as well as SCTP associations                                    |
| closed             | When the port is shown as closed, the TCP protocol indicates that the packdt we received back contains an RST flag. This scanning method can also be used to determine if our target is alive or not |
| filtered           | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target                |
| unfiltered         | This state of a port only occurs during the TCP-ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed                                             |
| open \| filtered   | If we do not get a response for a specific port, Nmap will set it to that state. This indicates that a firewall or packet filter may protect the port                                                |
| closed \| filtered | This state only occurs in the IP ID idle scan and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall                                              |


## Good to know 

- Finding NSE scripts `locate scripts/citrix` or whatever else you search

**TCP-SYN scan (-sS)**
- Receiving SYN-ACK -> Port is open
- Receiving RST -> Port is closed 
- No packet at all -> Filtered (by firewall or anything else)


## Host Discovery

```sh
# Scanning network range, with disabled port scan and output it to tnet file (This is Host discovery with ICMP)
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

# Take a file with a list of hosts -> host.lst
sudo nmap 10.129.2.0/24 -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

# disable portscanning (-sn), store result in hosts (-oA), perform ping scan using ICMP Echo requests (-PE), Show all packets sent and received (--packet-trace)
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace

# Same but provide reason for state
sudo nmap 10.129.2.18 -sn -oA host -PE --reason

# disable the arp ping + trace the packets
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping

# Clear view of Syn scan by disabling the ICMP echo request (-Pn), DNS resolution (-n) and the ARP ping scan (--disable-arp-sping)
sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-ping 
```


## Output Formats

- `-oN` -> Normal nmap output
- `-oG` -> Grepable output -> `.gnmap`
- `-oX` -> XML output 
- `-oA` -> Create all three output files
- `-v` / `-vv` -> Verbose; logs ports as they are found
- XML can be converted to HTML: `xsltproc target.xml -o target.html`


## Nmap Scripting Engine (NSE)

![[nmap_scripting_engine.png]]

```bash
# Default safe scripts
sudo nmap <target> -sC 

# Run scripts of a specific category
sudo nmap <target> --script <category> 

# Executing specific scripts (comma-separated)
sudo nmap <target> --script <script-name>,<script-name>
sudo nmap <target> -p 25 --script banner,smtp-commands

# Searching for Vulnerabilities 
sudo nmap <target> -p 80 -sV --script vuln
```


## Performance Tuning

- Speed → `-T <0-5>` (0 = paranoid, 5 = insane)
- Frequency → `--min-parallelism <number>`
- Timeouts → `--max-rtt-timeout <time>`
- Scan the top 100 ports → `-F`


## Firewall and IDS/IPS Evasion 
For Firewalls it's hard to block ACK scans (-sA), since usually the ACK packages are routed through. But the default SYN (-sS) or Connect (-sT) scan will easily be detected and dropped immediately.

- You can also create Decoy Scans -D RND:5 
	- With this method, we can generate random (`RND`) a specific number (for example: `5`) of IP addresses separated by a colon (`:`). Our real IP address is then randomly placed between the generated IP addresses.


DNS Proxying
By default, `Nmap` performs a reverse DNS resolution unless otherwise specified to find more important information about our target.


Sicko command to avoid detetion

`sudo nmap -g53 --max-retries=1 -Pn -p- --disable-arp-ping 10.129.2.47`


How to connect with netcat using a specific interface, going  via port 53, but connecting to port 50000

```shell
sudo nc -nv -s PWNIP -p53 STMIP 50000
```



## Frequently used commands 
```bash

## Search for all open Ports and the services  running there 
nmap -p- -sV <ip>

# Checking tcp ports 
nmap -sS <ip>

# Filtering for UDP 
nmap -sU <ip>

# Executing default scripts
nmap -sC <ip>

# Scanning for top N ports 
sudo nmap --top-ports N <ip>

# Output all formats -> Just the -oA part
# other outputs would be  -oN normal output, -oX XML output, -oS Script kiddie output, -oG grepable output (but deprecated, just use xml if you want to filter properly)
nmap -A -oA <basename> <ip>

# Searching for vulnerabilities on port 445 -> For example eternalblue. 
# You can check further categories 
sudo nmap -p 445 --script vuln <ip> 

# More specific category filter with only running scripts that are vuln and safe
sudo nmap -p 445 --script "safe and vuln" <ip>

# Running an nmap script
nmap --script <script-name> -p<port> <host>

# Default Scripts, Version on port 32 
sudo nmap -sC -sV -p 21 192.168.2.142

# Performing an FTP bounce back 
sudo nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2

# Banner grabbing
nc -nv <ip> 21
```

## Advanced nmap stuff

```bash
# Filter first for the relevant ports and then do a nmap for Service and scripts on the discovered ports 
ports=$(sudo nmap -p- --min-rate=1000 -T4 <ip> | grep '^[0-9]' | cut -d '/' -f 1 | tr '\n' ',' | sed 's/,$//')
sudo nmap -sC -sV -p "$ports" <ip>
```


## Links 

- [Host Discovery Strategies](https://nmap.org/book/host-discovery-strategies.html)
- [Table for TTL to OS Types](https://ostechnix.com/identify-operating-system-ttl-ping/)
- [Port Scanning Techniques](https://www.hackercoolmagazine.com/port-scanning-techniques-for-beginners/)
- [Bypassing firewalls/IDS](https://nmap.org/book/man-bypass-firewalls-ids.html)
