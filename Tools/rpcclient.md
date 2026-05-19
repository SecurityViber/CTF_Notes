---
tags: [basics, tool, smb, rpc]
---


> [!info] See also: [[SMB]] · [[smbclient]] · [[NetExec]]


Samba's interactive client for talking to MS-RPC interfaces over SMB. Indispensable for AD enumeration via SAMR/LSARPC.

## Connecting

```bash
# Null session (most useful flag for unauthenticated enum)
rpcclient -U "" -N <ip>

# With credentials
rpcclient -U <user>%<password> <ip>

# Pass-the-hash
rpcclient -U <user> --pw-nt-hash <NTLM-hash> <ip>
```

## Server / Domain Info

```
srvinfo                      # server info (OS, version)
enumdomains                  # list domains hosted
querydominfo                 # general domain info
getdompwinfo                 # password policy
```

## User Enumeration (SAMR)

```
enumdomusers                 # list domain users with RIDs
queryuser <RID>              # full user info (e.g. queryuser 0x3e9 → RID 1001)
querydispinfo                # display name + RID list (often readable when enumdomusers blocked)

# Lookup names ↔ SIDs
lookupnames <user>
lookupsids <SID>
```

## Group Enumeration

```
enumdomgroups
querygroup <RID>
querygroupmem <RID>
enumalsgroups domain         # aliases (local groups)
```

## Shares

```
netshareenumall              # full share list including hidden ($)
netsharegetinfo <share>      # ACL + comment
```

## RID Cycling 

When `enumdomusers` is denied but `lookupsids` works, walk RIDs manually:

```bash
for i in $(seq 500 1100); do
  rpcclient -U "" -N <ip> -c "lookupsids S-1-5-21-<domain-sid>-$i" 2>/dev/null
done
```

`enum4linux-ng` automates this on top of rpcclient.
