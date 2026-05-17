# Enumereation 

## Port Scanning 
After doing a first Port scan using 

```bash
sudo nmap -A -T4 10.129.21.41 -oA ./scans/bashed_scans
```

I discovered that Port 80 as a singe port is open. When opening up this website a static website appears. Nothing I can do there. 

## Dirbusting 
using the tools `ffuf`, `dirsearch` and also `zaproxy spider` .
```bash
# Dirsearch
dirsearch --url http://10.129.21.41       

# Fuff
ffuf -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt:FUZZ -u http://10.129.21.41/js/FUZZ  -t 300
```

I discovered different paths which do exist. One of the interessting paths was the `http://IP/dev/`. Under this path there was a `phpbash.php` file, which directly opend up a webshell. No need to upload anything. Using the webshell we were logged in as the `www-data` user. And we were already able to get the user flag.


# PrivEsc

When doing `sudo -l` revealed that we can execute every command as the `scriptmanager`. When looing around the filesystem, I discovered that there is the directory `/scripts` in which there is a `test.py` script and also an output file of `test.txt`. It seems that this script gets executed from time to time on a regular basis. Since scriptmanager has writepermission to `test.py` I was able to write a python reverse shell into the script and listen on my machine for a connection. Like this I became root.


