---
tags: [basics, tool, snmp]
---


> [!info] See also: [[SNMP]] · [[snmpwalk]] · [[onesixtyone]]


Mass SNMP scanner — much faster than `snmpwalk` because it doesn't track state between queries and pipelines requests in parallel. Less polite but ideal for bulk OID grabs once you know the community.

## Syntax

```bash
braa <community>@<target>:<OID>
```

## Examples

```bash
# Walk a subtree (wildcards via *)
braa public@10.10.10.10:.1.3.6.1.*

# Walk the system OID
braa public@10.10.10.10:.1.3.6.1.2.1.1.*

# Range syntax — query the same OID across many hosts
braa public@10.10.10.0-255:.1.3.6.1.2.1.1.5.0

# Multiple OIDs in one shot (comma-separated)
braa public@<ip>:.1.3.6.1.2.1.25.1.6.0,.1.3.6.1.2.1.25.4.2.1.2.*
```

## Output

Lines like:
```
10.10.10.10:1.3.6.1.2.1.1.5.0:6t=DESKTOP-XYZ
```

The `6t=` prefix denotes the SNMP type (`6t` = OCTET STRING). Strip with awk for clean values:

```bash
braa public@<ip>:.1.3.6.1.4.1.77.1.2.25.* | awk -F'=' '{print $2}'
```
