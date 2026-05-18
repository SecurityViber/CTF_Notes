---
tags: [basics, technique]
---

[[Basics/Index|← Basics]]


Here some common encoding and decoding commands.

## Base64

```bash
# Encode
echo -n "hello" | base64
echo -n "hello" | base64 -w 0   # no line wrapping (for payloads)

# Decode
echo "aGVsbG8=" | base64 -d

# Encode a file
base64 -w 0 shell.elf > shell.b64

# Decode a file
base64 -d shell.b64 > shell.elf
```

```powershell
# Encode string (PowerShell)
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes("IEX(...)"))

# Decode
[System.Text.Encoding]::Unicode.GetString([Convert]::FromBase64String("aQ..."))
```

## URL Encoding

```bash
# Using python
python3 -c "import urllib.parse; print(urllib.parse.quote('hello world'))"

# Decode
python3 -c "import urllib.parse; print(urllib.parse.unquote('hello%20world'))"
```

## Hex

```bash
# Encode to hex
echo -n "hello" | xxd -p

# Decode from hex
echo "68656c6c6f" | xxd -r -p

# Hex dump of binary
xxd shell.elf | head
```

## CyberChef (browser-based)
Useful for chaining transformations: base64 → URL decode → XOR → etc.



## Links and Resources 

- [CyberChef](https://gchq.github.io/CyberChef/)