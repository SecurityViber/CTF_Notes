
## Oracle TNS 

- Default Port: TCP/1521
- In short, the client-side Oracle Net Services software uses the `tnsnames.ora` file to resolve service names to network addresses, while the listener process uses the `listener.ora` file to determine the services it should listen to and the behavior of the listener.

Stuff to install to be able to do proper enumeration 

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome

```


The SIDs are an essential part of the connection process, as it identifies the specific instance of the database the client wants to connect to. If the client specifies an incorrect SID, the connection attempt will fail. Database administrators can use the SID to monitor and manage the individual instances of a database. For example, they can start, stop, or restart an instance, adjust its memory allocation or other configuration parameters, and monitor its performance using tools like Oracle Enterprise Manager.

There are various ways to enumerate, or better said, guess SIDs. Therefore we can use tools like `nmap`, `hydra`, `odat`, and others. Let us use `nmap` first.

### Footprinting 


```bash
# Nmap to get started 
sudo nmap -p1521 -sV 10.129.204.235 --open
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

# Using the odat script to enumerate -> btw, this can take a shitton of time
./odat.py all -s 10.129.204.235


# Once we got an account you can use sqlplus -> Afther login use sqplus to enumerate the database
sqlplus <username>/<password>@<ip>/<service-name>


# SQL Plus extrating password hashes by 
SQL> select name, password from sys.user$;

# If the server has a web server running you could upload a webshell for example
# to the directory /var/www/html
./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

```


