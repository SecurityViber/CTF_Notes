---
tags: [basics, tool, smtp, mail]
---


> [!info] See also: [[SMTP]] · [[Mail]] · [[smtp-user-enum]]


SWAKS — "Swiss Army Knife for SMTP". Scriptable SMTP test client. Far more flexible than `telnet` for sending crafted messages, testing relays, and validating auth.

## Basic Tests

```bash
# Minimal test (will use 127.0.0.1:25)
swaks --to user@target.com

# Specify the server
swaks --to user@target.com --server <ip>

# Force a sender + custom HELO
swaks --to user@target.com --from attacker@example.com --helo attacker.example.com --server <ip>
```

## Sending with a Body / Subject / Attachment

```bash
# Plain body
swaks --to victim@target.com --from sender@target.com --server <ip> \
      --header "Subject: Test" --body "Hello"

# Attach a file (HTML body + attachment)
swaks --to victim@target.com --from sender@target.com --server <ip> \
      --header "Subject: Invoice" --body-mime "<html>...</html>" \
      --attach-name invoice.pdf --attach @invoice.pdf
```

## Authentication

```bash
# AUTH LOGIN with user/pass
swaks --to victim@target.com --server <ip> --auth LOGIN \
      --auth-user <user> --auth-password <password>

# Force a specific auth mechanism
swaks --to victim@target.com --server <ip> --auth PLAIN --auth-user user --auth-password pwd
```

## TLS / SSL

```bash
# Opportunistic STARTTLS
swaks --to victim@target.com --server <ip> --tls

# Force SMTPS on 465
swaks --to victim@target.com --server <ip>:465 --tls-on-connect
```

## Relay Test

A useful misconfiguration check — can you send mail from arbitrary external sender → external recipient?

```bash
swaks --to external@elsewhere.com --from anyone@anywhere.com --server <target-ip>
```

If accepted, the target is an open relay.

## VRFY / EXPN (user enumeration)

```bash
# Quiet, just probe a single user
swaks --to user@target.com --quit-after RCPT --server <ip>
# Look at the response code for RCPT TO — 250 = exists, 550 = unknown
```

For batch VRFY/EXPN sweeps prefer [[smtp-user-enum]].

## Useful Flags

- `--show-raw-text` — see exactly what's on the wire
- `--no-data-fixup` — don't normalize line endings
- `--protocol SMTP` (default) / `--protocol ESMTP` / `--protocol LMTP`
- `--quit-after MAIL|RCPT|DATA` — leave session early to isolate behaviors
