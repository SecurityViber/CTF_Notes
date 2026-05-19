---
tags: [basics, technique, file-transfer]
---


> [!info] See also: [[Certifications/CPTS/File Transfers|File Transfers (CPTS — detailed)]]



## Spinning Up File Servers 

```bash
# Python HTTP Server with given port and network interface. For the interface provide the IP which is assigned to the desired interface 
python3 -m http.server <PORT> --bind <INTERFACE> 
```
## Sending Files 

```bash
# Using scp to transfer files 
scp linenum.sh user@remotehost:/tmp/linenum.sh
```

## Pulling Files 

```sh
# General wget download 
wget "https://filelocation"

# Wget download to a specific location 
wget -O /path/to/directory/file.ext <URL>
```

## Validating File integrity 
```bash
#Using md5sum to validate
md5sum shell

# Validate the File type 
file <filename>
```