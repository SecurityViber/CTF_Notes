---
tags: [basics, tool, rdp]
---


> [!info] See also: [[RDP]] · [[xfreerdp3]]


Perl script that audits an RDP service for weak configuration. Useful for client reporting and for confirming whether MITM / downgrade attacks are feasible.

Repo: https://github.com/portcullislabs/rdp-sec-check

## Install

```bash
git clone https://github.com/portcullislabs/rdp-sec-check.git
sudo cpan install Encoding::BER  # one-time dependency
```

## Usage

```bash
# Single target (default port 3389)
rdp-sec-check.pl <ip>

# Custom port
rdp-sec-check.pl <ip>:3390

# Many hosts from a file
rdp-sec-check.pl -f targets.txt
```

## What It Checks

- Supported encryption levels (None / Low / Client-Compatible / High / FIPS)
- Supported security layers (Standard RDP, TLS, CredSSP)
- Whether NLA (Network Level Authentication) is enforced
- Detected vulnerabilities (e.g. MS12-020-style weak crypto)

## Reading the Output

Look for:
- `RDP encryption supported: None`/`Low` → MITM with downgrade is trivial
- `NLA: NOT supported` → credential prompt is reachable pre-auth (BlueKeep-class risk; also enables brute-forcing without lockout in many configs)
- `Security layer: SSL/TLS only` → safer; CredSSP only → safer still
