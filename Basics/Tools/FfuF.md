
## Working with FFUF
```bash
# Directory fuzzing 
ffuf -w /usr/share/seclist/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ

# Extension Fuzzing -> Here we are assuming an index file and want to know what type of 
# Extensions it could have.
ffuf -w /usr/share/seclist/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ

# Page Fuzzing - Here we assume, that we know its a php site, so we want to find the directories for it 
ffuf -w /usr/share/seclist/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php

# Recursive Fuzzing - Also provide the file Ending and make it verbose 
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v


# Cool options to use -> filter for cods using mc, define and outputfile and the corresponding output format 
ffuf -w /usr/share/seclist/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php -mc 200 -o results.json -of json

# Sub-domain Fuzzing 
ffuf -w /usr/share/seclist/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
# VHost Fuzzing 
ffuf -w /usr/share/seclist/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'

# Parameter Fuzzing - GET
ffuf -w /usr/share/seclist/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx

# Paramter Fuzzing - POST
ffuf -w /usr/share/seclist/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin/.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx

```


## Finding required wordlists 

```bash

find -L /usr/share/wordlists -iname "*wp*" 2>/dev/null 

```