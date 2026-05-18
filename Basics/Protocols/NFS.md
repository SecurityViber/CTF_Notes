---
tags: [basics, protocol, nfs]
---

[[Basics/Index|← Basics]]

## Table of physical filesystems on an NFS server

```bash
# Config file listing exported shares
cat /etc/exports

# Show available NFS shares on a remote host
showmount -e <ip>

# Mounting an NFS share
sudo mount -t nfs <ip>:/ ./target-NFS/ -o nolock

# Unmounting
sudo umount ./target-NFS

# List mounted NFS shares with details
nmap --script nfs-ls,nfs-statfs,nfs-showmount -p 111,2049 <ip>
```
