---
tags: [basics, protocol, nfs]
---

[[Basics/Index|← Basics]]

# Table of physical filesystems onf an NFS server 
/etc/exports 

# Show possible nfs shares
showmount -e <ip>

# Mounting an nfs share
sudo mount -t nfs <ip>:/ ./target-NFS/ -o nolock

# Umounting
sudo umount ./target-NFS
