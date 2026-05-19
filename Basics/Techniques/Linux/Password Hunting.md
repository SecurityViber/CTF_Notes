---
tags: [basics, technique, linux, password]
---


> [!info] See also: [[Password Attacks]] · [[Password Cracking]]


## Authentication Mechanism
Most common used is PAM (Pluggable Authentication Modules).
- pam_unix.so


## Reading Passwords out of Memory 

```bash
# Tool to dump passwords from current linux desktop user - Similar to mimikatz 
git clone https://github.com/huntergregal/mimipenguin
mimipenguin.sh 

# 
```

## Common Files 

```bash
# Here you can find all the usernames
/etc/passwd
# Here you find all the hashes 
/etc/shadow
# Here you find old passwords to preven reuse 
/etc/security/opasswd

# Finding common config files 
for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

# Searching for passwords 
for i in $(find / -name *.cnf 2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i 2>/dev/null | grep -v "\#";done

# Searching for Databases 
for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";done

# Searching for notes 
find /home/* -type f -name "*.txt" -o ! -name "*.*"

# Searching for Scripts 
for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "doc\|lib\|headers\|share";done

# Enumerating Cronjobs 
cat /etc/crontab 

# Enumerating history files 
tail -n5 /home/*/.bash*

# Enumerating log files 
for i in $(ls /var/log/* 2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i 2>/dev/null;fi;done

```