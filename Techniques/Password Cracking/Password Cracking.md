---
tags: [basics, technique, password]
---


> [!info] See also: [[John The Ripper]] · [[Hashcat]] · [[Techniques/Password Cracking/Password Attacks]]



## Useful Terms 
- Rainbow Table -> large pre-compiled maps of inputs and ouput values for a given hash funcdiont
- Salting: Since ribow tables are such a powerfull attack, salting is used. A salt, in cryptographic terms, is a random sequence of bytes added t oa password before it is hashed. 
- Geco Field -> is a field in each record in the /etc/passwd file 


## Password Lists 

- [Crack Station Password Lists](https://crackstation.net/crackstation-wordlist-password-cracking-dictionary.htm)
- [Defaultcreds-cheat-sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet)



## Identifying a Hash 

```bash
# This can easily be done using hashcat 
hashcat '$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12' --identify

# or hashid 
hashid '$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12'

# or Hashidentifier 
hash-identifier '$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12'

# hashid also outputs the matching hashcat mode number
hashid '$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f' -m
```

## Generating Wordlists 
- [Crunch](https://www.kali.org/tools/crunch/) Can be used to create wordlists based on pattersn
- [CUPP](https://github.com/Mebus/cupp) - Common User Password Profiler -> Can also be used for password creation. It uses your OSINT outputs for that 
- [kwprocessor](https://github.com/hashcat/kwprocessor) is an advanced keyboard-walk generator.
- [Princeprocessor](https://github.com/hashcat/princeprocessor) - PRobability INfinite Chained Elements -> passowrd guessing algorithm to improve password cracking rates. 
- [CeWL](https://github.com/digininja/CeWL) - Tool which is crawling a website and crates you a pw list based on that 


```bash
# Using crunc to generate passwords for the following Pattern ILFREIGHTYYYYXXXX, whereas YYYY is a year and XXXX is the employee ID
crunch 17 17 -t ILFREIGHT201%@@@@ -o wordlist

# kwprocessor
kwp -s 1 basechars/full.base keymaps/en-us.keymap  routes/2-to-10-max-3-direction-changes.route

# CeWL 
cewl -d 5 -m 8 -e http://inlanefreight.com/blog -w wordlist.txt

```


## Extracting Hashes from files using John the Ripper 

```bash
# Installing John The Ripper 
sudo git clone https://github.com/magnumripper/JohnTheRipper.git

# Python porting of John the Ripper 
https://github.com/openwall/john/tree/bleeding-jumbo/run

# Extracting a hash from office document 
python office2john.py hashcat_Word_example.docx 

# Using hashcat to crack the created hash 
hashcat -m 9600 office_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

# Cracking Password protected zip files 
zip2john blueprints.zip

# Cracking KeePass File 
python keepass2john.py Master.kdbx

# Cracking protected PDF files 
python pdf2john.py inventory.pdf 

# Properly extracting the hash from an output 
7z2john hashcat.7z| cut -f2 -d ":" > hash.txt
```



## Tools to use 
- [pwntools](https://pypi.org/project/pwntools/) python package that can be used for CTF's regarding pwn challenges
- [Hashcat Documentation](https://hashcat.net/wiki/doku.php?id=example_hashes)
- [HashID](https://github.com/psypanda/hashID) Is an identifier tool, which helps you to figure out what hash you are working with
- See [[Hashcat]] for full hashcat workflows (rules, masks, hybrid attacks).
