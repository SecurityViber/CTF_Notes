---
tags: [basics, tool, smb]
---


> [!info] See also: [[SMB]] · [[NetExec]] · [[SMBMap]] · [[rpcclient]] · [[Impacket]]


Samba's command-line SMB client. Best for listing shares anonymously, mounting them, and basic file ops.

## Listing Shares 

```bash
# Null session (anonymous)
smbclient -N -L //<ip>

# With credentials
smbclient -L //<ip> -U <user>%<password>

# Specify domain
smbclient -L //<ip> -U DOMAIN/user%password
```

## Connecting to a Share

```bash
# Connect to a share interactively (null session)
smbclient -N //<ip>/<share>

# With credentials
smbclient //<ip>/<share> -U <user>%<password>

# Pass-the-hash (samba 4.x+)
smbclient //<ip>/<share> -U <user> --pw-nt-hash <NTLM-hash>
```

## Interactive Commands (once connected)

```
ls               # list files
cd <dir>         # change directory
get <file>       # download a file
put <file>       # upload a file
mget *.txt       # download multiple files (no prompts: prompt off; mget *)
mput *           # upload multiple files
recurse          # toggle recursive mode (combine with mget for whole dir)
prompt           # toggle interactive prompts
mask ""          # set match mask (combine with recurse + mget)
exit             # disconnect
```

## One-shot Commands

```bash
# Run a single command without entering the shell
smbclient -N //<ip>/<share> -c 'ls'

# Pull a whole directory recursively
smbclient -N //<ip>/<share> -c 'recurse; prompt; mget *'
```

## Tab Completion / TAR

```bash
# Backup a share to a tar file (useful for exfil)
smbclient //<ip>/<share> -U <user>%<password> -Tc backup.tar '*'
```
