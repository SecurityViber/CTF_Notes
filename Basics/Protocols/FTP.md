
## General Information 
- Port: 21
- TCP
- Configs:
	- `/etc/vsftpd.conf`
	- `/etc/ftpusers`

## Common Commands 

```bash
#Anonymous login
ftp -a 192.168.2.142

# Footprinting 
## Update first the nmap script db 
sudo nmap --script-updatedb

# Search for all the nse scripts which involve ftp
find / -type f -name ftp* 2>/dev/null | grep scripts  

# General script scan 
sudo nmap -sV -p21 -sC -A <ip address>

# Service interaction 
nc -zv <ip address> 21 
openssl s_client -connect <ipaddress> -starttls ftp

# Download complete directory from a ftp server
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136

```


```sh
# When connected via the fpt tool you can use the following commands

# Getting a single file 
get 


```

## Attack Vectors 

- FTP Bounce Back: https://www.geeksforgeeks.org/computer-networks/what-is-ftp-bounce-attack/
- Common misconfiguration with anonymous login

## Tools to interact with the service 
- ftp
- lftp
- ncftp
- filezilla
- crossftp
