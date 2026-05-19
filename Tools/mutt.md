---
tags: [basics, tool, mail]
---


> [!info] See also: [[Mail]] · [[SMTP]] · [[swaks]]


Terminal-based mail client. Useful when you need to send a message from a compromised host, read mailboxes via IMAP, or attach files in scripted social-engineering setups.

## Send with Attachment (one-shot)

```bash
# Plain body via stdin
echo "Body text" | mutt -s "Subject" -a invoice.pdf -- victim@target.com

# Send via a specific SMTP server (override muttrc)
echo "Body text" | mutt -s "Test" \
  -e "set smtp_url=smtp://user:pass@<server>:25" \
  -- victim@target.com
```

## Minimal `~/.muttrc` for SMTP

```muttrc
set realname = "Sender Name"
set from     = "sender@target.com"
set use_from = yes
set envelope_from = yes

set smtp_url = "smtp://user:pass@smtp.target.com:587/"
set ssl_starttls = yes
set ssl_force_tls = yes
```

## Read IMAP

```bash
mutt -f imaps://user:pass@mail.target.com
```

Inside, `q` quits, `d` deletes, `r` reply, `T` tag pattern, `;` group operation.
