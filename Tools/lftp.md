---
tags: [basics, tool, ftp]
---


> [!info] See also: [[FTP]]


More capable FTP client than the standard `ftp` — supports parallel transfers, mirroring, scripting, and many protocols (FTP, FTPS, SFTP, HTTP, HTTPS).

## Connecting

```bash
# Anonymous
lftp ftp://<ip>

# With creds
lftp -u <user>,<password> ftp://<ip>

# SFTP
lftp -u <user>,<password> sftp://<ip>

# One-shot command, then exit
lftp -u <user>,<password> -e "ls; quit" <ip>
```

## Useful Interactive Commands

```
ls                          # list remote
cd <dir>                    # change remote dir
lcd <dir>                   # change LOCAL dir
get <file>                  # download
mget <pattern>              # download many
put <file>                  # upload
mirror <remote>             # download whole directory tree
mirror -R <local>           # upload whole directory tree (the reverse)
queue <command>             # queue a transfer for parallel execution
set ftp:passive-mode on     # toggle passive
exit
```

## Recursive Mirror Examples

```bash
# Pull an entire remote directory locally
lftp -u <user>,<password> ftp://<ip> -e "mirror /remote/path /local/path; quit"

# Push a local directory to the server (reverse mirror)
lftp -u <user>,<password> ftp://<ip> -e "mirror -R /local/path /remote/path; quit"
```

## Scripting via stdin

```bash
lftp -u <user>,<password> ftp://<ip> <<'EOF'
set ftp:passive-mode on
cd /uploads
put payload.exe
quit
EOF
```
