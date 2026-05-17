
## Base commands to know
```bash
# Single Crack mode -> Cracking an entry from /etc/hosts 
john --single passwd

# Wordlist Attack 
john --wordlist=<wordlist> <hash_fil

# Incremental Mode -> powerful, brute-force-style password cracking mode 
john --incremental <hash_file>

# Config File 
cat /etc/john/john.conf

# Identify an hash using hashid and pipe it into a john REadable format 
hashid -j <hash>

# Unshadow a file if you are root user 
sudo cp /etc/passwd /tmp/passwd.bak 
sudo cp /etc/shadow /tmp/shadow.bak
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes 
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

## Cracking Files 

```bash
# Extracting the hash
zip2john ZIP.zip > zip.hash

# Cracking the hash
john --wordlist=rockyou.txt zip.hash

# cracking encrypted openssl files 
for i in $(cat rockyou.txt); do openssl enc --aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz; done

# Bitlocker 
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
## Then crack the hash using hashcat 
hashcat -a 0 -m 22100 backup.hash /usr/share/wordlist/rockyou.txt



```



## About tooling 
![[Pasted image 20251031221824.png]]