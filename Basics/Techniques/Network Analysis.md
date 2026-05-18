---
tags: [basics, technique, network]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Wireshark]] · [[Nmap]]



## Common Tools 
- tcpdump
- tshark
- Wireshark
- NGrep -> NGrep understands how to read live traffic or traffic from a pcap fila and utilize regex expressions and BPF syntax. Ideally for HTTP and FTP traffice
- tcpick ->paacket sniffer that specializes in tracking and reassembling tcp strams 



## Generel Networking commands
```sh
# ss - socket statistics; And shows t-tcp, l-listening, p-processes, n-numeric
ss -tlpn

# Show all sockets - listening and connected
ss -tpn


## Gettin Network Statistics using vnstat -> can also bsmb
 tweaked to different intervals 
vnstat -l <interface_name> 
```


## Tshark commands 

```sh 

```