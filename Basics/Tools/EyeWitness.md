---
tags: [basics, tool, recon]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Enumerating Website Information - Fingerprinting]]


Tool for screenshotting web pages, RDP services, and VNC servers at scale. Useful during recon to quickly identify what's running on each open HTTP/S port without visiting each one manually.

Repo: https://github.com/RedSiege/EyeWitness

```bash
# Screenshot all URLs from a file
eyewitness -f urls.txt --web

# Screenshot from nmap XML output
eyewitness -x nmap_scan.xml --web

# Screenshot with timeout and threads
eyewitness -f urls.txt --web --timeout 10 --threads 10

# Output to a specific directory
eyewitness -f urls.txt --web -d /tmp/eyewitness_output
```

The output is an HTML report with screenshots and headers for each host — useful for quickly spotting login pages, default installs, admin panels, and other interesting targets.
