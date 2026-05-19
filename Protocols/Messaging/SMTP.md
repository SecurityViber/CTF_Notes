---
tags: [basics, protocol, smtp, mail]
---


> [!info] See also: [[Mail]] · [[swaks]] · [[smtp-user-enum]]


- Accepts request on Port 25, but newer versions take port 587
- DKIM - DomainKeys
- SPF - Sender Policy Framework 
- `/etc/postfix/main.cf`
- You can try to reach out to an SMTP server using `telnet <ip> 25`
	- By this you can actually send an Email... wtf?!!
- Session can be initialized by `HELO/EHLO`

![[smtp_session_overview.png]]

### Footprinting

```bash
sudo nmap <ip> -sC -sV -p25
sudo nmap <ip> -p25 --script smtp-open-relay -v

# Enumerating users 
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <ip>
```


## Tools to interact with the service 

- [[swaks]] — Swiss Army Knife for SMTP (sending, auth, relay, TLS testing)
- [[smtp-user-enum]] — VRFY/EXPN/RCPT user enumeration
- See [[Mail]] for client-side tooling
