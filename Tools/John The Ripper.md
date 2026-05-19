---
tags: [basics, tool, password-cracking]
---


> [!info] See also: [[Password Cracking]] · [[Hashcat]] · [[Techniques/Password Cracking/Password Attacks]]


## Modes Overview

```bash
# Single Crack mode -> Cracking an entry from /etc/hosts 
john --single passwd

# Single Crack Mode with format
john --format=<hash_type> <hash or hash_file>

# Wordlist Attack 
john --wordlist=<wordlist> <hash_file>

# Wordlist mode with rules
john --wordlist=<wordlist_file> --rule <hash_file>

# Incremental Mode -> powerful, brute-force-style password cracking mode 
john --incremental <hash_file>

# Config File 
cat /etc/john/john.conf

# Identify an hash using hashid and pipe it into a john Readable format 
hashid -j <hash>

# Unshadow a file if you are root user 
sudo cp /etc/passwd /tmp/passwd.bak 
sudo cp /etc/shadow /tmp/shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes 
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```


## Cracking Files 

Generic workflow: extract the hash with a `*2john` helper, then crack.

```bash
# Find all *2john helpers on the system
locate *2john*

# Generic workflow
<tool> <file_to_crack> > file.hash
john --wordlist=<wordlist.txt> file.hash
```

### Examples

```bash
# Zip
zip2john ZIP.zip > zip.hash
john --wordlist=rockyou.txt zip.hash

# PDF
pdf2john server_doc.pdf > server_doc.hash
john server_doc.hash

# cracking encrypted openssl files 
for i in $(cat rockyou.txt); do openssl enc --aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz; done

# Bitlocker 
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
## Then crack the hash using hashcat 
hashcat -a 0 -m 22100 backup.hash /usr/share/wordlist/rockyou.txt
```


## About tooling 

![[john_helper_tools.png]]


![[john_helper_tools_list.png]]
