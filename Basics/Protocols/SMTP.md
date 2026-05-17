
- Accepts request on Port 25, but newer versions take port 587
- DKIM - DomainKeys
- SPF - Sender Policy Framework 
- `/etc/postfix/main.cf`
- You can try to reach out to an SMTP server using `telnet <ip> 25`
	- By this you can actually send an Email... wtf?!!
- Session can be initialized by `HELO/EHLO`

![[Pasted image 20250120191855.png]]

### Footprinting

```bash
sudo nmap <ip> -sC -sV -p25
sudo nmap <ip> -p25 --script smtp-open-relay -v

# Enumerating users 
smtp-user-enum
