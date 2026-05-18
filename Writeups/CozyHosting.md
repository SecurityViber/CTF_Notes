---
tags: [writeup, htb, linux]
---

[[Writeups/Overview|← Writeups]]


I mean it was a so called "easy" box. But in the beginning everything is a bit harder I guess :P. Since I have this VIP+ version, I used the Guided Mode. Because sometimes if you don't know, you don't know and at least I know then in which direction to look for. Anyway, about the box:

SPOILER: 

Simple scanning to discover port 80 is open,
Then I did some dirbusting, to discover that there was an endpoint called actuator (Element from Java Spring boot), where I found some SessionID, with which. Iwas able to login as the unser kanderson to the Web Portal,
On the webportal there was a field, where you were able to enter a hostname and a user to do an ssh check or something like that. Using ZAProxy, I repeted the request (endpoint: /executessh).,
I tried to discover if I get some command execution there, but struggled for a longtime and had to get some help from an old ippsec video. Once I understood (more or less) how I was able to get some execution, I uploaded a bash reverse shell. which gave me access to a low priv user.,
With the low priv user I was looking around the system and discovered a config file related to the running application, where login credentials for a Postgresql server were listed.,
Using the psql command I discovered the users database, in which there were some hashed secrets. One secret for the user admin.,
Then using some hashcat to crack the password.,
Based on the /etc/passwd file, I saw a few candidates of users, to which the password could match. By tring them out, I got access to another low priv user.,
using sudo -l showed me, that I could use ssh as sudo user. Then I used gtfobins to escalate and got the root flag.,

Privesc was easy, but the Foothold was a ride 