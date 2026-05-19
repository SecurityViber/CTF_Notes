---
tags: [basics, tool, snmp]
---


> [!info] See also: [[SNMP]] · [[onesixtyone]] · [[braa]]


Walks an SNMP MIB tree starting at a given OID. Part of `snmp` / `snmp-utils` packages.

## Basic Walks

```bash
# SNMPv2c, community "public", walk full tree
snmpwalk -v 2c -c public <ip>

# Walk a specific OID
snmpwalk -v 2c -c public <ip> 1.3.6.1.2.1.1.5.0

# Verbose with -O flags (don't print full OIDs, print numerically)
snmpwalk -v 2c -c public -On <ip> 1.3.6.1.2.1
```


## High-Value OIDs

| OID                       | Contents                                         |
| ------------------------- | ------------------------------------------------ |
| `1.3.6.1.2.1.1`           | System info (sysDescr, sysName, sysContact)      |
| `1.3.6.1.2.1.1.5`         | System name                                      |
| `1.3.6.1.2.1.25.1.6`      | System processes (count)                         |
| `1.3.6.1.2.1.25.4.2.1.2`  | Running processes (names)                        |
| `1.3.6.1.2.1.25.6.3.1.2`  | Installed software                               |
| `1.3.6.1.2.1.25.2.3.1.4`  | Storage info                                     |
| `1.3.6.1.4.1.77.1.2.25`   | User accounts (Windows)                          |
| `1.3.6.1.2.1.6.13.1.3`    | TCP local ports                                  |
| `1.3.6.1.2.1.4.20.1.1`    | IP addresses                                     |
| `1.3.6.1.4.1.9.9.392`     | Cisco config tree (often dumps `running-config`) |


## SNMPv3 (authenticated)

```bash
# noAuthNoPriv (user only)
snmpwalk -v 3 -u <user> -l noAuthNoPriv <ip>

# authNoPriv
snmpwalk -v 3 -u <user> -l authNoPriv -a SHA -A <auth-pass> <ip>

# authPriv (with encryption)
snmpwalk -v 3 -u <user> -l authPriv -a SHA -A <auth-pass> -x AES -X <priv-pass> <ip>
```


## Common Combos

```bash
# Get list of community strings to try with onesixtyone
snmpwalk -v 2c -c public <ip> 1.3.6.1.2.1.1.5.0 || snmpwalk -v 1 -c public <ip> 1.3.6.1.2.1.1.5.0

# After finding a community, dump everything to a file
snmpwalk -v 2c -c <community> <ip> > snmp_dump.txt
```
