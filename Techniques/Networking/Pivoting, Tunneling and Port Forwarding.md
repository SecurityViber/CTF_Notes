---
tags: [basics, technique, pivoting, tunneling, port-forwarding]
---


> [!info] See also: [[ProxyChains]] · [[Proxies]]



## About those topics 
**Pivoting** is moving to other networks through a compromised host to find more targets on different network segments. 

Those compromised hosts are also called:
- Pivot Host
- Proxy
- Foothold
- Beach Head system
- Jump Host

**Tunneling** is a subset of pivoting. Tunneling encapsulates network traffic into another protocol and routes trafffic through it.

One practical example for tunneling:
One way we used Tunneling was to craft our traffic to hide in HTTP and HTTPS. This is a common way we maintained Command and Control (C2) of the hosts we had compromised within a network. We masked our instructions inside GET and POST requests that appeared as normal traffic and, to the untrained eye, would look like a web request or response to any old website. If the packet were formed properly, it would be forwarded to our Control server. If it were not, it would be redirected to another website, potentially throwing off the defender checking it out.


![[nat_vs_dnat_comparison.png]]


Each box has different interfaces and route tables. 
See routing tables using 

```bash
netstat -r 
ip route
```

## Local SSH Port Forwarding 

![[ssh_local_port_forwarding.png]]

In the image you see, that on the victim server there is a mysql application, which is only reachable via the Victim Servers localhost. We can do an ssh Port forwarding to directly speek to the database going over ssh. For this we have to assigne some port to it (example: 1234). The command to do so is: 

```bash
# -L stands for local port forwarding
ssh -L 1234:localhost:3306 ubuntu@10.129.202.64

# Confirming the port forwarding with netstat 
netstat -antp | grep 1234

# Confirming the port forwarding with nmap 
nmap -v -sV -p1234 localhost

# You can also forward multiple ports 
ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```

## Dynamic Port forwarding 

![[ssh_dynamic_port_forwarding.png]]

On the attacking host, we have to setup a SOCKS5 listener on port 9050. We can initialize this by adding this with the ssh command. After the command we have an open port on our attack machine. All the traffic we forward through that port, will be executed by the vicitim machine itself! 

```bash
# Dynamic Port forwarding
ssh -D 9050 ubuntu@10.129.15.50
```

Now we have the open port, but we need a tool such as  `proxychain` to forward the tcp traffic over this port. 

```bash 
# Checking the proxychain configuration
tail -4 /etc/proxychains.conf
```

Then you should see some config such as: 

```conf
# meanwile
# defaults set to "tor"
socks4  127.0.0.1 9050
```

With those configurations, we can start to forward nmap traffic like the following 

```bash 
# Using nmap with proxychains
proxychains nmap -v -sn 172.16.5.1-200

# Using msfconsole with proxychains 
proxychains msfconsole 

# Using xfreerdp via proxychains
proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```


**Important Note**. When you want to access multiple ports use the dynamic portforwarding, which then requires your application to use proxychains. If you do the local portforwarding, you don't have to use proxychains, but rather you can access the applications via localhost. But use the convention for the localhost stuff to prefix the port with 1 such as 3389 -> 13389.



## Remote/Reverse Port Forwarding with SSH 


![[ssh_reverse_port_forwarding.png]]


```bash 
# Configure a listener on the Pivot Host to forwrad all the traffic to port 8080 to the attackersmachine port 8000 -> Which in our case will be metasploit
ssh -R <internalIPofPivotHost>:8080:0.0.0.0:8000
```

## Doing the same stuff using Metasploit 

-> Just come back to this module later on to understand. But as far Is I understood, it should just be an easier way for all the portforwarding we did manually before


## Socat 
Socat is a bidirectional relay tool that can create pipe sockets between 2 independent network channels without needing to use SSH tunneling. 
With Socat ssh tunneling is not required. 


![[socat_redirection_bind_shell.png]]


![[socat_pivot_metasploit.png]]


## SSH for Windows: plink.exe
Plink, short for PuTTY Link, is a Windows command-line SSH tool that comes as part of the PuTTY package when installed. 

**Windows bases Attack Host**

![[plink_ssh_socks_forwarding.png]]

To tunnel the traffic through this tunnel, a tool called `Proxifier` is then used (similar to proxychains on Linux). 

After configuring the `Proxifier` we can then directly start and rdp session using the `mstsc.exe`


## SSH Pivoting with Sshuttle 
- removes the need to configure proxychains.
- This too only works for pivoting over SSH and does not provide other options for pivoting over TOR or HTTPS proxy servers.

Example 

```bash

# We configure for which network we would like to route the traffic through the tunnel 
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 -v 

# What the sshuttle command is doing is to create some entries in iptables to redirect all the traffic to the network 172.16.5.0/23 via pivot host.
```

## Web Server Pivoting with Rpivot 
Rpivot is a revese SOCKS proxy tool written in Python for SOCKS tunneling. 

![[rpivot_socks_proxy.png]]


## Detection and Prevention 

