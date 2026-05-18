---
tags: [basics, tool, proxy, pivoting]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Pivoting, Tunneling and Port Forwarding]] · [[Proxies]]


Proxychains in combination with Tor can be used to hide your identity. 
To use proxychains you have to configure the file under `/etc/proxychains4.conf`

```sh
#socks4 127.0.0.1 9050
http 127.0.0.1 8080
```

After configuration proxychain can be used like the following 

```sh
# This will route the traffic in quiet mode through our proxy chain
proxychains -q curl http://google.ch
```