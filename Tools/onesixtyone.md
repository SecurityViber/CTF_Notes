---
tags: [basics, tool, snmp, brute-force]
---


> [!info] See also: [[SNMP]] · [[snmpwalk]] · [[braa]]


Fast SNMP community-string brute-forcer. Sends UDP requests in parallel — significantly faster than walking with snmpwalk for discovery.

## Basic Usage

```bash
# Single host, community list from file
onesixtyone -c community-strings.list <ip>

# Multiple hosts (one IP per line)
onesixtyone -c community-strings.list -i hosts.txt
```

## Common Wordlists

- `/usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt`
- `/usr/share/seclists/Discovery/SNMP/snmp.txt`

## Tuning

```bash
# Quiet mode (only valid hits)
onesixtyone -c community.list -q <ip>

# Wait n ms between packets (slow down to avoid drops)
onesixtyone -c community.list -w 50 <ip>
```

## Typical Workflow

1. Brute community strings with onesixtyone.
2. Confirm + enumerate with [[snmpwalk]].
3. For bulk OID retrieval (faster), use [[braa]].
