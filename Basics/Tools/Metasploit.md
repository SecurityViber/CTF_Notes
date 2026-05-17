
## Useful commands 

```bash
# Searching for modules 
search type:auxiliary http html title tag

# Use specific Modules 
use auxiliary/scanner/http/title
 ```
 

## General Configuration

```sh
# Configuring a proxies for metasploit -> could be combined with proxychain stuff or for burpsuite
set PROXIES HTTP:127.0.0.1:8080
```


## About Metasploit 
- Typical Path  `/usr/share/metasploit-framework/modules`
	- There you find modules such as: 
		- Auxiliary
		- encoders
		- evasion
		- exploits
		- nops
		- payloads
		- post

![[MetasploitOverview.png]]