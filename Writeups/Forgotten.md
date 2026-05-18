---
tags: [writeup]
---

[[Writeups/Overview|← Writeups]]


# General Info Dump

Port scan:
- Port 80,22 open 
- On Port 80 no domain name 

Using ffuf for directory busting:
- discovering:
	- /survey
	- /server-status

On /survey runs the LimeSurvey installer:
- Installation process setting:
	- user: dummy; database password: password; database name: dumbo

