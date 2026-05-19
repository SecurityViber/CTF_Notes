---
tags: [basics, tool, password-cracking]
---


> [!info] See also: [[Password Cracking]] · [[John The Ripper]] · [[Techniques/Password Cracking/Password Attacks]]


Hashcat is a GPU-accelerated password cracker that supports many hash types and attack modes.

## Attack Modes

| Mode | Name              | Description                                |
| ---- | ----------------- | ------------------------------------------ |
| 0    | Straight          | Wordlist                                   |
| 1    | Combination       | Wordlist × wordlist                        |
| 3    | Brute Force       | Mask                                       |
| 6    | Hybrid (W+M)      | Wordlist + mask                            |
| 7    | Hybrid (M+W)      | Mask + wordlist                            |


## Basic usage

```bash
# Getting example hashes for testing 
hashcat --example-hashes

# Running a Benchmark Check 
hashcat -b -m 0

# Identify a hash
hashcat '$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12' --identify

# Hashcat Syntax 
hashcat -a 0 -m <hash type> <hash file> <wordlist>

# Combination Attack
hashcat -a 1 -m <hash type> <hash file> <wordlist1> <wordlist2> 

# Mask Attacks -> Used to generate words matching a specific pattern; -a 3 means it's a brute force attack here
hashcat -a 3 -m 0 md5_mask_example_hash -1 01 'ILFREIGHT?l?l?l?l?l20?1?d'

# Hybrid Mode (a=6) - wordlist + mask
hashcat -a 6 -m 0 hybrid_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt '?d?s'

# Hybrid Mode (a=7) - mask + wordlist
hashcat -a 7 -m 0 hybrid_hash_prefix -1 01 '20?1?d' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```


## Cracking unshadowed Linux hashes

```bash
sudo cp /etc/passwd /tmp/passwd.bak
sudo cp /etc/shadow /tmp/shadow.bak

unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes

# Hashcat cracking unshadowed hashes (mode 1800 = sha512crypt)
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```


## Rules

You can create a rule file to apply mutations to each word in the wordlist.

![[hashcat_rules.png]]

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

Create a mutated wordlist from a base list:

```bash
hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
```

Custom rule example — uppercase, leetspeak substitutions, append year:

```bash
# rule: uppercase, substitute o→0 i→1 e→3 s→5 a→@ and then append 2019
echo 'c so0 si1 se3 ss5 sa@ $2 $0 $1 $9' > rule.txt
echo 'password' > test.txt 

# Generate passwords using the rule (no cracking)
hashcat -r rule.txt test.txt --stdout

# Crack hashes using the rule
hashcat -a 0 -m 100 hash /usr/share/seclist/Passwords/LeakedPasswords/rockyou.txt -r rule.txt 
```

Default ruleset location: `/usr/share/hashcat/rules`


## Cracking specific file types

```bash
# Office hash (Word, Excel)
python office2john.py hashcat_Word_example.docx > office_hash
hashcat -m 9600 office_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

# BitLocker (mode 22100)
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
hashcat -a 0 -m 22100 backup.hash /usr/share/wordlist/rockyou.txt

# WPA/WPA2 (mode 22000)
hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```


## Wordlist generation (CeWL)

[CeWL](https://github.com/digininja/CeWL) crawls a website and creates a wordlist:

```bash
cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
wc -l inlane.wordlist
```


## Links

- [Hashcat hash modes reference](https://hashcat.net/wiki/doku.php?id=example_hashes)
