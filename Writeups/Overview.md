---
tags: [writeup, index]
---

[[Home]]



| Box                          | Notes                                                                                                                                                                          | Last rooted |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- |
| [[Bashed]]                   | Linux box. Port 80 only. Found a phpbash webshell via directory busting, used it as foothold. PrivEsc via sudo and cron jobs.                                                  |             |
| [[CozyHosting]]              | Linux box. Spring Boot actuator leaked session token → web portal access → OS command injection via `/executessh` → reverse shell → postgres creds → hashcat → SSH sudo PrivEsc. |             |
| [[Forgotten]]                | Linux box. LimeSurvey installer on port 80 was exploitable for initial foothold.                                                                                               |             |
| [[Kobold]]                   | Linux box. MCPJam RCE (GHSA-232v-j27c-5pp6) for foothold, then docker group abuse to mount host filesystem and read root flag.                                                 |             |
| [[Return]]                   | Windows box.                                                                                                                                                                   |             |
