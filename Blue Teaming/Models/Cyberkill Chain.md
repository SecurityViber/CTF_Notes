## Cyber Kill Chain

The 7 stages an attacker goes through:

![[cyberkillchain.png]]

| # | Stage | Summary |
| - | ----- | ------- |
| 1 | Reconnaissance | Choose a target and gather information |
| 2 | Weaponization | Build the initial-access malware and embed it in an exploit |
| 3 | Delivery | Get the payload to the victim |
| 4 | Exploitation | Payload is triggered, code runs on the target |
| 5 | Installation | Stager runs and sets up persistence |
| 6 | Command & Control | Attacker gets remote access to the machine |
| 7 | Actions on Objectives | Exfiltration, privilege escalation, etc. |

### 1. Reconnaissance

- Initial stage
- Attacker chooses a target and performs information gathering (OSINT etc.)
- Can be **active** or **passive**:

![[active_vs_passive_recon.png]]

### 2. Weaponization

- Malware for initial access is developed and embedded into some type of exploit
- Malware is lightweight and should not be detected by AV or EDR
- Goal is to provide persistent remote access

### 3. Delivery

- Exploit/payload gets delivered to the victim
- Usually phishing via email or phone

### 4. Exploitation

- The moment the exploit or delivered payload is triggered
- Attacker attempts to execute code on the target system to gain access or control

### 5. Installation

- Initial stager is executed and running on the compromised machine
- Typical techniques: **Droppers**, **Backdoors**, **Rootkits**

| Technique | Purpose | How it gets there |
| --------- | ------- | ----------------- |
| Dropper | Small piece of code that installs malware on the system and executes it | Email attachments, malicious websites, social engineering |
| Backdoor | Gives the attacker ongoing access; used for further attacks or data theft | Installed during exploitation, or delivered through a dropper |
| Rootkit | Hides its presence on the system to evade AV and other security tools | Installed during exploitation, or delivered through a dropper |

### 6. Command & Control (C2)

- Attacker establishes a remote access capability to the compromised machine

### 7. Actions on Objectives

- Data exfiltration, gaining higher privileges, etc.
