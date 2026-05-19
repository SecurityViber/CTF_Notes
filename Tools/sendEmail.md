---
tags: [basics, tool, mail, smtp]
---


> [!info] See also: [[SMTP]] · [[Mail]] · [[swaks]]


Lightweight Perl script for sending email from the command line. Less flexible than [[swaks]] but ubiquitous and trivially scriptable for phishing payload delivery.

## Basic Usage

```bash
sendEmail -f sender@target.com \
          -t victim@target.com \
          -s <smtp-server>:25 \
          -u "Subject line" \
          -m "Body text"
```

## With Authentication

```bash
sendEmail -f sender@target.com \
          -t victim@target.com \
          -s smtp.target.com:587 \
          -xu <user> -xp <password> \
          -o tls=yes \
          -u "Hello" -m "Body"
```

## With Attachment

```bash
sendEmail -f sender@target.com -t victim@target.com \
          -s <smtp-server>:25 \
          -u "Invoice" -m "See attached." \
          -a /tmp/invoice.pdf
```

## Multiple Recipients

```bash
sendEmail -f sender@target.com \
          -t "alice@target.com,bob@target.com" \
          -s <smtp-server>:25 \
          -u "Notice" -m "Body"
```

## Tips

- For HTML bodies, pass `-o message-content-type=html`.
- Read body from a file: `-o message-file=/path/to/body.txt`.
- Use `-l logfile.txt` to capture the full SMTP exchange when debugging.
