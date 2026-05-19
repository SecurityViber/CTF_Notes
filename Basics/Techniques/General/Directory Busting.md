---
tags: [basics, technique, web, recon]
---


> [!info] See also: [[FfuF]]


For directory busting there are multiple tools out there. Here a few you could use. 



## Dirsearch

Sidenote: Dirsearch is usally the easiest way to do some initial dirbusting. 

```sh
# Some useful configs you can do for your dirsearch 
dirsearch -o outputFile --format json -u <url>

# Including specific status codes 
dirsearch --include-status=200,404,500-599

# Exclude specific status codes 
dirsearch --exclude-status=404
```

## Gobuster 
Can be used to perform DNS, vhost, and directory brute-forcing. 

```bash
# Simple directory busting 
gobuster dir -u http://<ip>/ -w /usr/share/seclists/Discovery/Web-Content/common.txt

# DNS busting also can be done with gobuster
gobuster dns -d inlanefreight.com -w /usr/share/SecLIsts/Discovery/DNS/namelist.txt
```


## FFuF
