---
tags: [basics, protocol, database, oracle]
---


> [!info] See also: [[ODAT]] · [[sqlplus]]


## Oracle TNS 

- Default Port: TCP/1521
- In short, the client-side Oracle Net Services software uses the `tnsnames.ora` file to resolve service names to network addresses, while the listener process uses the `listener.ora` file to determine the services it should listen to and the behavior of the listener.

The SIDs are an essential part of the connection process, as it identifies the specific instance of the database the client wants to connect to. If the client specifies an incorrect SID, the connection attempt will fail. Database administrators can use the SID to monitor and manage the individual instances of a database. For example, they can start, stop, or restart an instance, adjust its memory allocation or other configuration parameters, and monitor its performance using tools like Oracle Enterprise Manager.

There are various ways to enumerate, or better said, guess SIDs. Therefore we can use tools like `nmap`, `hydra`, `odat`, and others.

### Footprinting 


```bash
# Nmap to get started 
sudo nmap -p1521 -sV 10.129.204.235 --open
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

# Using the odat script to enumerate -> btw, this can take a shitton of time
./odat.py all -s 10.129.204.235


# Once we got an account you can use sqlplus -> After login use sqlplus to enumerate the database
sqlplus <username>/<password>@<ip>/<service-name>


# SQL Plus extracting password hashes by 
SQL> select name, password from sys.user$;

# If the server has a web server running you could upload a webshell for example
# to the directory /var/www/html
./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

```


## Tools to interact with the service 

- [[ODAT]] — auditing/attack framework (SID/account brute force, file R/W, RCE)
- [[sqlplus]] — Oracle's official interactive SQL CLI (Instant Client)
