---
tags: [basics, tool, ftp]
---


> [!info] See also: [[FTP]] · [[lftp]]


Friendlier FTP client than the default `ftp` — auto-completes paths, supports background transfers, and remembers bookmarks. Bundled with helpers (`ncftpget`, `ncftpput`) for one-shot transfers.

## Interactive

```bash
# Anonymous
ncftp <ip>

# With creds
ncftp -u <user> -p <password> <ip>
```

Once connected: standard `ls`, `cd`, `get`, `mget`, `put`, `mput`, `bgget`, `bgput` (background transfers).

## One-shot Helpers

```bash
# Download a single file (anonymous)
ncftpget ftp://<ip>/file.txt

# Download with creds
ncftpget -u <user> -p <password> <ip> ./localdir /remote/path/file.txt

# Upload a single file
ncftpput -u <user> -p <password> <ip> /remote/path local.txt

# Recursive download
ncftpget -R -u <user> -p <password> <ip> ./localdir /remote/dir
```
