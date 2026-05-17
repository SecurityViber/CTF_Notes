
 ```bash 
rdp-sec-check.pl <FQDN/IP>
xfreerdp /u:<user> /p:"<password>" /v:"<FQDN/IP"
```



| `rdp-sec-check.pl <FQDN/IP>`                                  | Check the security settings of the RDP service. |
| ------------------------------------------------------------- | ----------------------------------------------- |
| `xfreerdp /u:<user> /p:"<password>" /v:<FQDN/IP>`             | Log in to the RDP server from Linux.            |
| `evil-winrm -i <FQDN/IP> -u <user> -p <password>`             | Log in to the WinRM server.                     |
| `wmiexec.py <user>:"<password>"@<FQDN/IP> "<system command>"` |                                                 |
|                                                               |                                                 |

